# Zephyr's blinky Sample: Add System Workqueue

1) Add Workqueue definition in _main.c_

       static struct k_work_delayable blink_led_work;

2) Create Workqueue function handler

       static void blink_led_work_fn(struct k_work *work)
       {
           int ret;
           ret = gpio_pin_toggle_dt(&led);
           if (ret < 0) {
               return;
           }
           k_work_schedule(&blink_led_work, K_MSEC(SLEEP_TIME_MS));
       }

3) Create a function to initialze the Workqueue

       static void work_init(void)
       {
           k_work_init_delayable(&blink_led_work, blink_led_work_fn);
       }

4) Remove the <code>while(1)</code> loop in main() function and replace with Workqueue

           work_init();
           k_work_schedule(&blink_led_work, K_NO_WAIT);
