# Rutronik/Nordic Semiconductor Workshop: <br> _Wireless IoT Entwicklung mit Nordic Semiconductor_
## Workshop Dates/Locations

> 5.May 2026, Volketswil <br>
> 16.April 2026, Hamburg <br>
> 26.February 2026, Donaueschingen <br>
> 3.February 2026, Dreieich 


## Code Snippets for Hands-On Sessions

In this repository, you will find the files you need for the hands-on session. 

1) Introduction to IDE and Blinky Application
   - Build the Zephyr's __blinky__ Sample => see slides
   - [Zephyr's blinky Sample: Change the LED pin in DeviceTree](HandsOn1-2_deviceTree.md)
   - [optional: Add System Workqueue](HandsOn1-3_workqueue.md)
   - [optional: Adding custom KCONFIG definitions](HandsOn1-4_kconfig.md) 
   
2) Bluetooth Peripheral LED & Button Service (LBS)
   - [Adding Bluetooth beacon functionality](HandsOn2-1_beacon.md)
   - Build Nordic's _Bluetooth LE LED Button Service_ (nrf/samples/bluetooth/peripheral_lbs) => see slides
   - [Changing Advertisement information](HandsOn2-3_scanResponse.md)
   - [optional: Using LED0 as connection indicator](HandsOn2-4_connection.md)
     
4) Nordic PMIC & Fuel Gauge Integration
   - [Adding nPM2100 Fuel Gauge](HandsOn3-1_pmic.md)   
   - [Adding standard BLE Battery Service (BAS)](HandsOn3-2_bas.md)


## Workshop Slides

- [Einleitung von Nordic zum _nRF Connect SDK_](presentation/00_Nordic_Introduction.pdf)
- [Hands-on Kapitel 1: IDE Einführung & Blinky Application](presentation/01_Hands-on_Blinky.pdf)
- [Hands-on Kapitel 2: Bluetooth Peripheral LED & Button Service (LBS)](presentation/02_Hands-on_LBS_Service.pdf)
- [Hands-on Kapitel 3: Nordic PMIC & Fuel Gauge Integration](presentation/03_Hands-on_Nordic_PMIC.pdf)
