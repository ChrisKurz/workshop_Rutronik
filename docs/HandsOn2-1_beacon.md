# Bluetooth Sample: Add Basic Bluetooth Functionality => Beacon

1) Add to _prj.conf_: SW configuration, enable Bluetooth (BT) Stack

       CONFIG_BT=y

2) Add to _main.c_: include Bluetooth header files

       #include <zephyr/bluetooth/bluetooth.h>

3) Add to _main.c_: add Bluetooth advertising structure using macros. __Use your own Device Name here!!!__

       /* The BLE adv packet can hold a total of 31 bytes, 3 bytes are taken to indicate BT data flags (LE only, */
       /* no BR/EDR support) + 2 bytes are taken to indicate BT complete local Name (0x09) and the length of the */
       /* name. This leaves a maximum of 26 bytes left for the actual device name */

       #define DEVICE_NAME "My BT Device"

       static const struct bt_data ad[] = {
           BT_DATA_BYTES(BT_DATA_FLAGS, (BT_LE_AD_GENERAL | BT_LE_AD_NO_BREDR)),
           BT_DATA(BT_DATA_NAME_COMPLETE, DEVICE_NAME, sizeof(DEVICE_NAME)-1),
       };

5) Add to _main.c_: initialize the Bluetooth stack and start Advertising in <code>int main(void)</code> function

           // enable and init BTLE stack
           int err = bt_enable(NULL);

	         // start Advertising as non-connectable
           err = bt_le_adv_start(BT_LE_ADV_NCONN, ad, ARRAY_SIZE(ad), NULL, 0);
           if (err) {
           printk("Advertising failed to start (err %d)\n", err);
               return -1;
           }
           printk("Advertising started\n");
   
