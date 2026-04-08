# Bluetooth Sample: Changing Advertisement information

1) _main.c_: Change the advertisement information from "Service UUID" to a website using a BT Uniform Resource Identifier

	   #define URL_STRING "//academy.nordicsemi.com"
       static const struct {
           unsigned char prefix; 
           const char url_string[sizeof(URL_STRING)];
       } url_data = {
           .prefix = 0x17,
           .url_string = URL_STRING
       };

       static const struct bt_data sd[] = {
           BT_DATA(BT_DATA_URI, (const unsigned char *)&url_data, sizeof(url_data))
       };
   
