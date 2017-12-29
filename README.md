# sonoff-testing

1. [如何改firmware](http://blog.rniemand.com/changing-sonoff-firmware-visual-guide/)，其中要注意這一段話，NOTE: To put the Sonoff into flash mode you will need to hold down the power switch while powering it on! <span style="color:red;">若要進入燒錄模式，先按下Sonoff的按鈕後再插USB燒錄線</span>
2. USB燒錄線可以用這些[晶片](https://github.com/arendst/Sonoff-Tasmota/wiki/Hardware-Preparation)。You'll furthermore need a USB UART TTL 3.3V Converter/Programmer (e.g. CP2102, CH340G, FT232, PL2303).
3. 注意user_config.h要修改MQTT_HOST和WIFI
4. IRMQTTServer.ino要修改MQTT_SERVER
5. 燒錄好之後，重新拔插USB，開Serial Monitor，可以用VS Code的Console看Log
