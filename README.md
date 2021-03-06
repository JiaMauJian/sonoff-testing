# sonoff Wifi會斷斷續續
應該是焊壞了[Wifi stops working](https://github.com/arendst/Sonoff-Tasmota/wiki/Troubleshooting)
```
00:00:33 WIF: Connecting to AP2 ooxx157 in mode 11N as sonoff-4681...
00:00:40 WIF: Connect failed as AP cannot be reached
00:00:40 WIF: WPSConfigactive for 1 minute
00:00:59 APP: Restarting

 ets Jan  8 2013,rst cause:1, boot mode:(3,5)

load 0x4010f000, len 1384, room 16 
tail 8
chksum 0x2d
csum 0x2d
v4ceabea9
~ld

```

# user_config.h更新沒反應

2019/7/15 新版tasmota即可用網頁設定Wifi不用改user_config.h

1. 需修改user_config.h中的CFG_HOLDER的值(跟之前燒錄的不一樣即可)
2. [#define STA_SSID2 not recognised](https://github.com/arendst/Sonoff-Tasmota/issues/376)

# sonoff flash

1. [如何改firmware](http://blog.rniemand.com/changing-sonoff-firmware-visual-guide/)，其中要注意這一段話，NOTE: To put the Sonoff into flash mode you will need to hold down the power switch while powering it on!若要進入燒錄模式，先按下Sonoff的按鈕後再插USB燒錄線
2. USB燒錄線可以用這些[晶片](https://github.com/arendst/Sonoff-Tasmota/wiki/Hardware-Preparation)。You'll furthermore need a USB UART TTL 3.3V Converter/Programmer (e.g. CP2102, CH340G, FT232, PL2303).
3. 注意user_config.h要修改MQTT_HOST和WIFI (user_config.h)
4. 燒錄好之後，重新拔插USB，開Serial Monitor，可以用VS Code的Console看Log

# sonoff 4ch pro flash

1. USB燒錄線和板子接線 3.3V <-> 3.3V, TX <-> RX, RX <-> TX, GND <-> GND
2. 請志揚幫忙接中間那條[黑線](https://projetsdiy.fr/wp-content/uploads/2017/09/sonoff-4ch-pro-esp8285-flash-mode.jpg)，GND接到晶片上方的第二隻腳，USB燒路線插上去，3秒後放開就可以開始燒錄

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
 - 2019/7/15 不用那麼麻煩寫程式了，只要用把sonoff 4ch pro的GPIO設定成Counter當Input訊號即可。
 - 起因需要讀取鐵捲門控制箱的燈號(CNT4)[百業 BT-FD101](https://github.com/JiaMauJian/sonoff-testing/blob/master/bt-fd101%20fast%20rolling%20door%20of%20control%20receiver%20function%20operation%20manual.pdf)
 - 先拿Sonoff Basic測試
 - 將Arduino Uno Pin13設定為OUTPUT，每0.5秒閃一次
 - 改Sonoff-Tasmota-5.10.0 sonoff.ino程式，將Sonoff Pin14設定為INPUT，狀態有變就輸出 [程式](https://github.com/JiaMauJian/sonoff-testing/blob/master/sonoff_%E6%94%B9GPIO%E6%B8%AC%E8%A9%A6.ino)
 ```
 ...
  pinMode(gpio_test_pin, INPUT);
 ...
  // 狀態有改變再輸出
  if (digitalRead(gpio_test_pin) == HIGH && pre_gpio_test_pin_state == 0)
  {
    pre_gpio_test_pin_state = 1;
    Serial.println(1);    
  }
  if (digitalRead(gpio_test_pin) == LOW  && pre_gpio_test_pin_state == 1)
  {
    pre_gpio_test_pin_state = 0;
    Serial.println(0);    
  }
```

# 送訊息給Domoticz (MQTT)
```
char msg_gpio_test_pin[50];
...
void loop()
{
  ...
  // 狀態有改變再輸出
  if (digitalRead(gpio_test_pin) == HIGH && pre_gpio_test_pin_state == 0)
  {
    pre_gpio_test_pin_state = 1;
    Serial.println(1);    
    snprintf (msg_gpio_test_pin, 50, "{\"idx\":28, \"nvalue\":0, \"svalue\":\"1\"}");
    Serial.print("Publish message for msg_gpio_test_pin: ");
    Serial.println(msg_gpio_test_pin);
    MqttClient.publish("domoticz/in", msg_gpio_test_pin);
  }
  if (digitalRead(gpio_test_pin) == LOW  && pre_gpio_test_pin_state == 1)
  {
    pre_gpio_test_pin_state = 0;
    Serial.println(0);
    snprintf (msg_gpio_test_pin, 50, "{\"idx\":28, \"nvalue\":0, \"svalue\":\"0\"}");
    Serial.print("Publish message for msg_gpio_test_pin: ");
    Serial.println(msg_gpio_test_pin);
    MqttClient.publish("domoticz/in", msg_gpio_test_pin);
  }

  ...
}
```
