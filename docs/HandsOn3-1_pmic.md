# nPM2100 Sample: Adding nPM2100 Fuel Gauge

  > __NOTE:__  Output voltage of nPM2100EK is 3.0V. So, please change VDD on nRF54L15DK to 3000mV. => _nRF Connect for Desktop_ => _Board Configurator_

1) Create DTS overlay file (if not existing already): nrf54l15dk_nrf54l15_cpuapp.overlay

2) Add to nrf54l15dk_nrf54l15_cpuapp.overlay: selected pins for the I2C peripheral use:

       &pinctrl {
           i2c21_default: i2c21_default {
               group1 {
                   psels = <NRF_PSEL(TWIM_SDA, 1, 11)>,
                           <NRF_PSEL(TWIM_SCL, 1, 12)>;
                           bias-pull-up;
               }; //GPIO Port 1, Pin 11 & 12
	         };

           i2c21_sleep: i2c21_sleep {
               group1 {
                   psels = <NRF_PSEL(TWIM_SDA, 1, 11)>,
                           <NRF_PSEL(TWIM_SCL, 1, 12)>;
                           low-power-enable;
                   };
              };
       };

3) Add to nrf54l15dk_nrf54l15_cpuapp.overlay: enable i2c21 peripheral and add npm2100-ek

       &i2c21 {
           status = "okay";
           pinctrl-0 = <&i2c21_default>;
           pinctrl-1 = <&i2c21_sleep>;
           pinctrl-names = "default", "sleep";

           // list i2c attached peripherals
           npm2100ek_pmic: pmic@74 {
               compatible = "nordic,npm2100";
               reg = <0x74>;

               npm2100ek_vbat: vbat {
                   compatible = "nordic,npm2100-vbat";
               };
           };
       };

4) Add to prj.conf: SW configuration, enable libraries for fuel gauge

       CONFIG_SENSOR=y
       CONFIG_NRF_FUEL_GAUGE=y
       CONFIG_NRF_FUEL_GAUGE_VARIANT_PRIMARY_CELL=y
       CONFIG_REQUIRES_FLOAT_PRINTF=y

5) Add to main.c: include headers

       #include <zephyr/kernel.h>
       #include <zephyr/device.h>
       #include <zephyr/drivers/sensor.h>
       #include <nrf_fuel_gauge.h>

6) Add to main.c: include global variables for fuel gauge

       // Global variables for fuel gauge
       static int64_t ref_time;
       static const struct device *vbat = DEVICE_DT_GET(DT_NODELABEL(npm2100ek_vbat));
       static const struct battery_model_primary FG_model = {
           #include <battery_models/primary_cell/AA_Alkaline.inc>
       };
       /* Basic assumption of average battery current.
        * Using a non-zero value improves the fuel gauge accuracy, even if the number is not exact.
        */
       static const float battery_current = 5e-3f;

7) Add to main.c: internal functions to retrieve sensor values, initialize the fuel gauge, and update the algorithm periodically

       static int read_sensors(const struct device *vbat, float *voltage, float *temp)
       {
           struct sensor_value value;
           int ret;

           ret = sensor_sample_fetch(vbat);
           if (ret < 0) {
               return ret;
           }

           sensor_channel_get(vbat, SENSOR_CHAN_GAUGE_VOLTAGE, &value);
           *voltage = (float)value.val1 + ((float)value.val2 / 1000000);

           sensor_channel_get(vbat, SENSOR_CHAN_DIE_TEMP, &value);
           *temp = (float)value.val1 + ((float)value.val2 / 1000000);

           return 0;
       }

       int fuel_gauge_init(const struct device *vbat)
       {
           struct nrf_fuel_gauge_init_parameters parameters = {
               .model_primary = &FG_model,
               .i0 = 0.0f,
               .opt_params = NULL,
           };
           int ret;

           printk("nRF Fuel Gauge version: %s\n", nrf_fuel_gauge_version);

           ret = read_sensors(vbat, &parameters.v0, &parameters.t0);
           if (ret < 0) {
               return ret;
           }

           ret = nrf_fuel_gauge_init(&parameters, NULL);
           if (ret < 0) {
               return ret;
           }

           ref_time = k_uptime_get();

           return 0;
       }

       int fuel_gauge_update(const struct device *vbat) 
       {
           float voltage;
           float temp;
           float soc;
           float delta;
           int ret;

           ret = read_sensors(vbat, &voltage, &temp);
           if (ret < 0) {
               printk("Error: Could not read from vbat device\n");
               return ret;
           }

           delta = (float)k_uptime_delta(&ref_time) / 1000.f;
           soc = nrf_fuel_gauge_process(voltage, battery_current, temp, delta, NULL);
 
           printk("V: %.3f, T: %.2f, SoC: %.2f\n", (double)voltage, (double)temp, (double)soc);

           return 0;
       }

8) Add to main.c: int main(void) - Main Routine Integration

           printk("nPM2100 Fuel Gauge integration on %s\n", CONFIG_BOARD_TARGET);

           if (!device_is_ready(vbat)) {
               printk("vbat device not ready.\n");
               return -1;
           }
           printk("PMIC device ok, init fuel gauge\n");

           err = fuel_gauge_init(vbat);
           if (err < 0) {
               printk("Could not initialise fuel gauge.\n");
               return -1;
           }
           printk("Fuel gauge initialised using model %s\n", FG_model.name);
           // other initialisation functions

           // main infinite loop
           while (1) {
               fuel_gauge_update(vbat);
               k_msleep(1000);
           }
