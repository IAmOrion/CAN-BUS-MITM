# CAN BUS MITM

# INTRO
I created this DIY CAN BUS MITM filter out of necessity due to owning an aftermarket Audi TT Digital Cluster.  The firmware developers of this cluster, for some currently unknown reason, have the cluster spamming a diagnostic session - KWP2000. At power-up the cluster runs a fixed ~15-frame handshake:
0x10 DiagnosticSessionControl — asking for a non-standard session type (0x89)
0x1A ReadECUIdentification (legacy KWP-style) — asking for identification data sets 0x9B and 0x91
0x31 RoutineControl — starting three different routines (0xB8, 0xB9, 0xBA), with routine 0xB9 carrying a trailing byte that changes every cycle (32/34/37/35/29/28...) — possibly a live value or seed.  This opens a diagnostic session then never closes it, holding it open for no reason.  This results in the OBD port becoming "blocked" for diagnostic use.  VCDS and OBDEleven for example won't work whilst the cluster is connected.  At the time of editing this README there still has not been a firmware fix supplied despite 3 (poor) attempts and 1+ year(s) of waiting.  So out of frustration I created a hardware CAN BUS MITM (Man-In-The-Middle) device that could selectively block specific CAN BUS IDs and Data being sent or received and thus allowing the use of the cluster, blocking it's diagnostic request and allowing VCDS and OBDEleven to work perfectly again.

Whilst this DIY was born from my specific use case, the CAN BUS MITM itself could be used in any Vehicle a CAN BUS MITM filter is needed.
 
# HARDWARE
The Gerbers for my PCBs are in the [PCB Gerbers](https://github.com/IAmOrion/CAN-BUS-MITM/tree/main/PCB%20Gerbers) folder.

The PCB uses 2 microcontroller development boards (MCU boards) to make it easier for others to assemble their own.  I personally use JLCPCB (not affiliated) but PCBWay also offer PCB services.
JLCPCB is about £6-£8 for 5 PCBS (5 is min order qty) including cheapest global shipping rate (18+ days)

# STM32 
This is the can bus filter itself.  It can also record can bus sessions if needed but by design does not record or initiate the SD Card as default.  Keeping it's primary purpose to simply filter can data.

# ESP32
The ESP32 is used to create a WebUI and a BLE Receiver for the setup and settings.  It serves a web page or you can use the Android App and change various settings and add/remove blocks etc. 

I designed the board so that the 'average joe' could hopefully make their own.  Hence using off the shelf easy to get parts rather than designing a smaller, more compact, bespoke board.  So it's larger than a professional design, but it all fits fine so not a problem.

# STM32_ESP32 PCB
Top View:
![Top View:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/PCB%20Gerbers/STM32_ESP32_PCB_Top.png)

Bottom View:
![Top View:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/PCB%20Gerbers/STM32_ESP32_PCB_Bottom.png)

# 32 Pin Green Connector Breakout PCB
![Top View:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/PCB%20Gerbers/Audi_32Pin_Green_Breakout_Top.png)

# BOM (Bill of Materials)
WeActStudio STM32G474CEU6 Long Board:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/STM32.png)

1 x ESP32-C3:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/ESP32_C3.png)

1 x Mini560 Pro:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/Mini560Pro.png)

2 x SN65HVD230 IC:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/SN65HVD230%20.png)

2 x G6K-2Y-Y1 3v RELAY:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/Relay.png)

2 x MMTB3904 aka 1AM:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/mmtb3904.png)

1 x SD Card Module (Optional):
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/sdcard.png)

1 x 1808 Fuse + 1 x 1808 Fuse Holder:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/Fuse_Fuse_Holder.png)

1 x TVS:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/TVS.png)

1 x SS34 Input Diode: 
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/SS34.png)

2 x 1N4148 Relay Diode:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/1N4148.png)

Resistors (All "1206" package):
2 x 120Ω 1206
3 x 0Ω 1206
3 x 10kΩ 1206*
2 x 1kΩ 1206

* some (rare) Mini 560 Pro modules needed the 10k ENABLE resistor, most already have this onboard - so the 10kΩ EN* resistor as marked on the PCB may not be needed.

# 3D Printed Case / Base Plate

[STL Files](https://github.com/IAmOrion/CAN-BUS-MITM/tree/main/3D%20Print%20STL%20Files)

![32 Pin Green Connector Breakout Base:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/3D%20Print%20STL%20Files/32%20PIN%20Plug%20Base.png)

![STM32_ESP32_PCB CASE BOTTOM:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/3D%20Print%20STL%20Files/STM32%20CANBUS%20FILTER%20CASE%20BOTTOM.png)

![STM32_ESP32_PCB CASE_LID:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/3D%20Print%20STL%20Files/STM32%20CANBUS%20FILTER%20CASE%20LID.png)

# Android App (APK File) *(Currently No iOS App)* 

The Android app APK file is available to download in the [latest](https://github.com/IAmOrion/CAN-BUS-MITM/tree/main/latest) folder.

# STM32 Firmware
[STM32 Firmware here:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/latest/STM32Firmware.bin)

# ESP32-C3 Firmware
[ESP32-C3 Firmware available here:](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/latest/ESP32Firmware.bin)

*When you have an internet connection on your phone, The WebUI (STA mode, not AP) and BLE Android App check the latest folder, compare version again the relevant .txt file and can update to the latest version if there is one.*

# Source code

ESP32, STM32, Android App source code coming soon.
