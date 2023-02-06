# StratuxMultiStation


My intent is to combine multiple stratux units to maximize the range of my Attic based stratux setup and be able to pair it with a real IPAD based EFB utility (FltPlanGo and ForeFlight). 



I have one "full" Stratux : Stratux 1 ; and a second stratux (stratux 2 with only an SDR Stratux 2). 
Both are hardwired to my router (192.168.15.1) with static IP's:
  Stratux 1: 192.168.15.39 
  Stratux 2: 192.168.15.41

They also broadcast their individual wifi networks with security enabled.


Goals
-One Stratux to bring them all and in the darkness bind them:
  - Aka a Main stratux (Stratux 1) with feeders (Stratux 2) comprised of different SDR's.
  
Derivative Creative Process:

Lets setup a network of Stratux Receivers

Basic Setup:

Stratux 1
 Equipment:
  -GPS: USB u-blox 7 GNSS receiver (NMEA protocol) with a 6in USB extension Cable
  -AHRS unit
  SDR 0 : Tuned to 1090 MHZ with matching antenna
    -this shows up as a Realtek RTL2838UHIDIR 
  SDR 1 : Tuned to 978 MHZ with matching antenna
    - this shows up as a NooElec NESDR Nano 2
 Ground Plane: Unsupported Aluminum Foil.
 Stratux Fork: https://github.com/b3nn0/stratux
 FileSystem: Expanded with custom Systemd service 

  Enable Developer Mode (multiple tap/click stratux version until developer appears)
    -Enable Persistent Logging:
     -Menu -> Settings -> Diagnostics:
       Enable Persistent logging
      



Stratux 2:
 Equipment:
   -SDR 0 : Tuned to 1090 MHZ with matching antenna
    -Note this is an RTLSDR Blog V3 with a ridgid repurposed cookie tin as the ground plane.
   
 Stratux Fork: https://github.com/b3nn0/stratux
 FileSystem: Standard
 
 
 
 Commands Used:
  stxstop (stop stratux service which kills webpage)
  sdrs (list SDRS)
  
  program SDRs:
  
  rtl_eeprom - d 00 -s stx:1090:0
  rtl_eeprom - d 01 -s stx:978:0
  
  start stratux service that enables webpage
  stxstart
  
     
 Max Range Acheived: 
 Stratux 1: 90 miles
 Stratux 2 (Claimed, lots of doubt.jpg = 120 miles)
 
 
 Enable Sauron mode on Stratux 1 using Systemd:
 
 create a new systemd service in /lib/systemd/system/StratuxCombineInjestion.service 
 
 nano /lib/systemd/system/StratuxCombineInjestion.service 
 
[Unit]
Description=StratuxCombineInjestion
After=network.target
After=stratux.service

[Service]
ExecStart=/opt/stratux/bin/StratuxMultiStation
StandardOutput=journal
StandardError=journal
Restart=always
RestartSec=5 

[Install]
WantedBy=multi-user.target
 
Lets build the shell script that the systemd service uses to makes this happen:

nano /opt/stratux/bin/StratuxMultiStation
#!/bin/bash
nc 192.168.15.41 30005 | nc localhost 30004


My observations:
Beast format works best (port 30005)
RAW TCP port (30002) results in garbage data in the webpage. 


Periodic reboots will be required as Stratux by default runs in Read Only mode which becomexs filled with logs around 3 days. 


 

    
 
  
