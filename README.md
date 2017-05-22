# Raspbery Pi Zero W Timelapse Boot

This is a modified version of [Jeff Geerling's Pi Timelapse] (https://github.com/geerlingguy/pi-timelapse) project.  The script is run as a service at startup and is altered to output to a specific directory rather than current working directory.

#### 

1. Follow Jeff's great setup on his page linked above, including all dependencies.
2. Following [this guide by Matt](http://www.raspberrypi-spy.co.uk/2015/10/how-to-autorun-a-python-script-on-boot-using-systemd/), next we will create a configuration file (aka a unit file) that tells systemd what we want it to do and when :
```
sudo nano /lib/systemd/system/timelapse.service
```
Add in the following text :
```
[Unit]
Description=Timelapse V1
After=multi-user.target

[Service]
Type=idle
ExecStart=/usr/bin/python /home/pi/pi-timelapse/timelapse.py

[Install]
WantedBy=multi-user.target
```
You can save and exit the nano editor using [CTRL-X], [Y] then [ENTER].

This defines a new service called “Timelapse V1” and we are requesting that it is launched once the multi-user environment is available. The “ExecStart” parameter is used to specify the command we want to run. The “Type” is set to “idle” ensures the ExecStart command is only run when everything else has loaded. For my GPIO based scripts the default type of “simple” didn’t work.

Note that the paths are absolute and fully define the location of Python as well as the location of our Python script.

In order to store the script’s text output in a log file you can change the ExecStart line to :
```
ExecStart=/usr/bin/python /home/pi/pi-timelapse/timelapse.py > /home/pi/pi-timelapse/bootlog.log 2>&1
```
The permission on the unit file needs to be set to 644 :
```
sudo chmod 644 /lib/systemd/system/timelapse.service
```
3.
```
sudo systemctl daemon-reload
sudo systemctl enable myscript.service
```
Reboot the Pi and your custom service should run :
```
sudo reboot
```
Step 4 – Check status of your service.

You can check the status of your service using :
```
sudo systemctl status myscript.service
```

Once the service is running, I use WinSCP to SSH into my Pi to check the photos as the service is running.