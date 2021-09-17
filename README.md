# fand
Daemon to control fan speed on a Raspberry Compute Module 4 IO Board

This is a small proof of concept for controlling the fan effect depending on the CPU temperature.
It works with Raspberry Compute Module 4 IO boards and supports standard 12V FANs with PWM drive.
The code is written in python and sets the fan speed by addressing the EMC2301 controller on the io board. Fan effect depends on CPU temparature and the temp/effect pairs are looked up in a simple config file. This works very well with my overklocked cm4 and a 40mm Noctua fan in a small enclousure. You can easily edit the mapping in the config file to fit your scenario.

I have this setup running 24/7 and CPU temperature is nicely maintained by the deamon. Setting all 4 cores to 100% with overvoltage (6) and 2ghz for 10 hours worked flawlessly.

My system is based on a 64bit Ubuntu desktop distro.



- The deamon file name: fand
- Config file name: default.map
- Both live in: /var/lib/fand
- The log file is: /var/log/fand.log
- The service definition is: /etc/systemd/system/fand.service

The python libraries time, smbus and logging are used.

## How to install

You have to do the steps below as root, i.e. use the sudo command

1. Activate I2C:
     **sudo raspi-config**
     Select Advanced Options -> I2C -> to enable the I2C driver.
2. Install missing packages:
     **apt install python3-smbus**
4. Create the **/var/lib/fand** directory
5. Copy the files fand and default.map to **/var/lib/fand**
6. Make the program executable:
     **chmod u + x /var/lib/fand/fand**
7. Copy the file **fand.service** to the **/etc/systemd/system directory**
8. Do a **systemctl daemon-reload**
9. Do a **systemctl enable fand** to enable the daemon
10. Check with **systemctl status fand**

If the daemon is installed correctly and running the result of systemctl status fand should look like:

● fand.service - fand: fan controller for Raspberry cm4io boards
     Loaded: loaded (/etc/systemd/system/fand.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2021-05-09 12:07:29 CEST; 1h 17min ago
   Main PID: 734 (python3)
      Tasks: 1 (limit: 8811)
     Memory: 7.4M
     CGroup: /system.slice/fand.service
             └─734 python3 /var/lib/fand/fand

It starts automatically when booting the system.

You can check how it works by using a simple shell command: **sudo watch -n 10 'vcgencmd measure_temp; i2cget -y 10 0x2f 0x30 | xargs printf "fan=%d"'**


Enjoy,
Tim
