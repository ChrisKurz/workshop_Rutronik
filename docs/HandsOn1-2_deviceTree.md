# Zephyr's _blinky_ Sample:  Change the LED pin in DeviceTree

1) Create overlay file: nrf54l15dk_nrf54l15_cpuapp.overlay

There are several options for changing the LED pin. Select one:

## Option A:  Modify led0 definition in overlay file
  
2)  Add to _nrf54l15dk_nrf54l15_cpuapp.overlay_:

        &led0 {
            gpios = <&gpio2 7 GPIO_ACTIVE_HIGH>;
        };

-----

## Option B:  Adding a new node group

2) Alternatively, add to _nrf54l15dk_nrf54l15_cpuapp.overlay_ a new node group:

        / {
            board_leds {
                compatible = "gpio-leds";        
                my_led_1: my_led1 {
                    gpios = <&gpio1 10 (GPIO_ACTIVE_HIGH)>;
                    label = "My Green LED 1";
                };
            };
        };

3) Modify in _main.c_, obtain access to the new led node group:

       #define LED0_NODE DT_PATH(board_leds, my_led1)

----- 

## Option C:  Adding an Alias

2) Add alias for the new custom led node group:

	     aliases {
		       led0 = &my_led_1;
	     };

3) Complete _nrf54l15dk_nrf54l15_cpuapp.overlay_ file:

	     / {
           board_leds {
               compatible = "gpio-leds";        
               my_led_1: my_led1 {
                   gpios = <&gpio1 10 (GPIO_ACTIVE_HIGH)>;
                   label = "My Green LED 1";
               };
           };
           aliases {
               led0 = &my_led_1;
           };
       };

4) Adapt in _main.c_: revert the node Access back to the initially used alias led0

       /* The devicetree node identifier for the "led0" alias. */
	     #define LED0_NODE DT_ALIAS(led0)
   
