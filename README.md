# CAN BUS MITM

# INTRO
I created this DIY CAN BUS MITM filter out of necessity due to owning an aftermarket Audi TT Digital Cluster.  The firmware developers of this cluster, for some currently unknown reason, have the cluster spamming a diagnostic session - KWP2000. At power-up the cluster runs a fixed ~15-frame handshake:
0x10 DiagnosticSessionControl — asking for a non-standard session type (0x89)
0x1A ReadECUIdentification (legacy KWP-style) — asking for identification data sets 0x9B and 0x91
0x31 RoutineControl — starting three different routines (0xB8, 0xB9, 0xBA), with routine 0xB9 carrying a trailing byte that changes every cycle (32/34/37/35/29/28...) — possibly a live value or seed.  This opens a diagnostic session then never closes it, holding it open for no reason.  This results in the OBD port becoming "blocked" for diagnostic use.  VCDS and OBDEleven for example won't work whilst the cluster is connected.  At the time of editing this README there still has not been an firmware fix supplied despite 3 (poor) attempts and 1+ year(s) of waiting.  So out of frustration I created a hardware CAN BUS MITM (Man-In-The-Middle) device that could selectively block specific CAN BUS IDs and Data being sent or received and thus allowing the use of the cluster, blocking it's diagnostic request and allowing VCDS and OBDEleven to work perfectly again.

# HARDWARE
The Gerbers for my PCBs are in the [PCB Gerbers](https://github.com/IAmOrion/CAN-BUS-MITM/tree/main/PCB%20Gerbers) folder.

The PCB uses 2 microcontroller development boards (MCU boards).
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
STM32G474CEU6:

1 x ESP32-C3:
![](https://github.com/IAmOrion/CAN-BUS-MITM/blob/main/BOM%20Photos/ESP32_C3.png)

Mini560 Pro:

SN65HVD230 IC:

G6K-2Y-Y1 3v RELAY:

MMTB3904 aka 1AM: 

SD Card Module:

1808 Fuse + 1808 Fuse Holder:

TVS:

1 x SS34 Input Diode: 

2 x 1N4148 Relay Diode:

Resistors (All "1206" package):
2 x 120Ω 1206
3 x 0Ω 1206
3 x 10kΩ 1206
2 x 1kΩ 1206
