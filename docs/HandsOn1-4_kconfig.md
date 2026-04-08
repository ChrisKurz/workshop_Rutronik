# Zephyr's blinky Sample: Add custom defined KCONFIG

1) Create a file with the name __Kconfig__. (no extension!)

	   menu "Blinky at Workshop"
       config BLINKY_TIME_ON
         int "The time the LED is on (mS)."
         default 1000
       endmenu
       menu "Zephyr Kernel"
       source "Kconfig.zephyr"
       endmenu

2) Add the following line to the _prj.conf_ file

       CONFIG_BLINKY_TIME_ON=500

3) Replace in the _main()_ functions while loop the line <code> k_msleep(SLEEP_TIME_MS);</code> by following line

       k_msleep(CONFIG_BLINKY_TIME_ON);
