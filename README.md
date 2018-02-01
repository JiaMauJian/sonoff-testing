# user_config.h更新沒反應

1. 需修改user_config.h中的CFG_HOLDER的值(跟之前燒錄的不一樣即可)
2. [#define STA_SSID2 not recognised](https://github.com/arendst/Sonoff-Tasmota/issues/376)

# sonoff flash

1. [如何改firmware](http://blog.rniemand.com/changing-sonoff-firmware-visual-guide/)，其中要注意這一段話，NOTE: To put the Sonoff into flash mode you will need to hold down the power switch while powering it on!若要進入燒錄模式，先按下Sonoff的按鈕後再插USB燒錄線
2. USB燒錄線可以用這些[晶片](https://github.com/arendst/Sonoff-Tasmota/wiki/Hardware-Preparation)。You'll furthermore need a USB UART TTL 3.3V Converter/Programmer (e.g. CP2102, CH340G, FT232, PL2303).
3. 注意user_config.h要修改MQTT_HOST和WIFI
4. IRMQTTServer.ino要修改MQTT_SERVER
5. 燒錄好之後，重新拔插USB，開Serial Monitor，可以用VS Code的Console看Log

# sonoff 4ch pro flash

1. 同上面sonoff basic的tasmota source code
2. 左下角要接3隻腳(TX, RX, GND)，這裡要注意的是TX接TX，RX接RX，不要懷疑[Normally, we have to reverse the jumpers between RX and TX but this is not the case on most Sonoff products because there is a misregistration on a PCB. Try first without reversing. If that does not work, reverse RX / TX](https://diyprojects.io/hack-sonoff-4ch-pro-firmware-mqtt-tasmota-inclusion-domoticz/#.Wksh51WWaM8)
3. 請志揚幫忙接中間那條[黑線](https://projetsdiy.fr/wp-content/uploads/2017/09/sonoff-4ch-pro-esp8285-flash-mode.jpg)，GND接到晶片上方的第二隻腳，然後就...接12V電壓器的電源，3秒後放開就可以開始燒錄

# GPIO Location
 * [sonoff 4ch pro ](https://github.com/JiaMauJian/sonoff-testing/blob/master/sonoff%20pro%204ch%20GPIO.jpg?raw=true)
 * [sonoff basic](https://github.com/JiaMauJian/sonoff-testing/blob/master/sonoff%20basic%20GPIO%20Location.jpg?raw=true)

# MQTT-Sonoff devices
 * tele -> Sonoff devices publish
 * stat -> Sonoff devices publish
 * cmnd -> Sonoff devices subscribe
 
# MQTT-如何用手機測試sonoff

1. cmnd/sonoff/power on (%topic%預設是sonoff可以改成其他名稱，避免控制到所有都叫sonoff的裝置)，https://github.com/arendst/Sonoff-MQTT-OTA-Arduino/wiki/MQTT-Features
2. [手機畫面1](https://github.com/JiaMauJian/sonoff-testing/blob/master/MQTT%20Client%201.png?raw=true)，[手機畫面2](https://github.com/JiaMauJian/sonoff-testing/blob/master/MQTT%20Client%202.png?raw=true)

# 如何把Arduino的Output餵給Sonoff當input
 - 起因需要讀取鐵捲門控制箱的燈號[百業 BT-FD101]()
