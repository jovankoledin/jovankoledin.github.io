---
layout: post
title: Light Phone
description:  Checking my phone takes me out of the flow state when I am trying to be productive at my computer. I put my phone out of sight when I want to stay focused, but in the back of my mind I am concerned I might miss an important notification. The Light Phone fixes these issues.

main-image: /default_vid.gif
---

---
# Light Phone
A LED display that moves a lava lamp like pattern across a screen and flashes red when you get a important iPhone notification.

## Quick Demo 

#### Default State
{% include image-gallery.html images="default_vid.gif" height="500" %}  

#### Important Notification State
{% include image-gallery.html images="notification_vid.gif" height="500" %}  

[See this for a full demo](https://github.com/jovankoledin/light_phone/blob/main/visuals/demo_higher_res.MP4)

## Hardware Up Close
{% include image-gallery.html images="front.jpg" height="400" %}
{% include image-gallery.html images="side.jpg" height="400" %}
{% include image-gallery.html images="leds.jpg" height="400" %}
{% include image-gallery.html images="inside.jpg" height="400" %}
### BLE Connection
{% include image-gallery.html images="ble_bigger.png" height="400" %}    

## How it Works
Inside is an ESP32 hooked up to a 16x16 WS2812b led display.
The C++ firmware in the ESP32 runs two RTOS tasks (update LED display, and run Bluetooth ops). The Bluetooth ops took the most time to get working out of anything on this project.
It is a Bluetooth Low Energy (BLE) protocol, specifically a Apple Notification Center Service (ANCS) Central & Peripheral device that can connect to your iPhone.
The BLE system before it connects acts as a Central device and searches for a ANCS Peripheral device (i.e. your iPhone) to connect to. Once the two devices find eachother the Light Phone subcribes to your iPhones ANCS service and connects. The BLE system then switches to being a Peripheral device and your iPhone becomes the Central device, the iPhone then streams notification data packets to your Light Phone.
When your Light Phone gets a notification data packet it parses it and checks to see if any of the important contacts are referenced in the notification. If an important contact is referenced,
the BLE task sets a notification received flag which triggers a flashing red pattern to pulse on the LED display for 30 sec. Once the red pattern is done flashing,
the Light Phone goes back to its default Lava Lamp display.

## Additional Feautures
- Implemented Wifi OTA update ability so I didnt have to plug my computer into my ESP32 to upload new firmware.
- Created a Github CI/CD pipeline so that when I pushed to Github it automatically compiled and uploaded the code onto my device through Wifi

[Repo link](https://github.com/jovankoledin/light_phone)
