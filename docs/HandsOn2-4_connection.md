# Bluetooth Sample: Using LED0 as connection identicator

We want to realize the following:
- LED0 starts blinking when not connected and advertising
- LED0 stops blinking and is OFF when connected.

There are different ways to realize this. Here two options are shown:

## Option A

1) We will use main's entire loop to handle LED.

           for (;;){
               if (app_connected_state){
                   dk_set_led_off(RUN_STATUS_LED);
               } else {
                   dk_set_led(RUN_STATUS_LED, (++blink_status) %2);
               }
               k_sleep(K_MSEC(RUN_LED_BLINK_INTERVAL));
           }

2) Declare the <code> app_connected_state </code> variable.

       static bool app_connected_state;

3) We have to set/clear <code>app_connected_state</code> in the appropriate Bluetooth callback functions.

  - in function <code>static void connected(struct bt_conn *conn, uint8_t err)</code>:

            app_connected_state = true;

  - and in function <code>static void disconnected(struct bt_conn *conn, uint8_t err)</code>:

            app_connected_state = false;


## Option B

1) Blinking of LED0 is only done in main entire loop, when the device is disconnected.

           for (;;){
               if (app_disconnected){
                   dk_set_led(RUN_STATUS_LED, (++blink_status) % 2);
               }
               k_sleep(K_MSEC(RUN_LED_BLINK_INTERVAL));
           }

2) Declare the variable <code>app_disconnected</code>.

       static bool disconnected = true;

3) The variable <code>app_disconnected</code> has to be set or cleared in the appropriate Bluetooth callback function

    - in function <code>static void connected(struct bt_conn *conn, uint8_t err)</code>:

              dk_set_led_off(RUND_STATUS_LED);
              app_disconnected = false;

   - and in function <code>static void disconnected(struct bt_conn *conn, uint8_t err)</code>:

             app_disconnected = true;
