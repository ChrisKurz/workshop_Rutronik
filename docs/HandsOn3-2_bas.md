# nPM2100 + Bluetooth: Adding standard BLE Battery Service (BAS)

1) create a Bluetooth Peripheral application, e.g. peripheral_lbs

2) add to prj.conf: enable BT BAS Service

       CONFIG_BT_BAS=y

3) add to main.c

       #include <zephyr/bluetooth/services/bas.h>

4) Update/set the BAS Service characteristic for the battery Level. This could be done inside of fuel_gauge_update() for example, with (float) soc being the state of charge, returned from the FG algorithm

       bt_bas_set_battery_level((uint8_t)soc);
