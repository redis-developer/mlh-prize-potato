# MLH Prize Potato
Congratulations on winning the MLH Hackathon! You are the owner of a Libre Potato, a Raspberry Pi Clone with 2Gb of RAM and plenty of compute power to run your own standalone Redis Stack server! As this is all open source, including the hardware, feel free to install your own operating system and create to your heart's content! We're excited that you decided to excel with us, and look forward to your continued growth and hacking achievements!

## About the Libre Potato
This unit comes preinstalled with [Raspian Linux](https://distro.libre.computer/ci/raspbian/11/2023-05-03-raspbian-bullseye-arm64%2Baml-s905x-cc.img.xz), a Linux OS designed specifically for compute boards like the Raspberry Pi and Libre Potato.  A Wireless Dongle is included, as well as an SD card containing the installed OS. All you need to do is plug in a mouse, keyboard, and monitor, and you'll be up and running in no time.

The Potato is powered by the provided micro USB cable. You'll want to connect it to 5 volt, 2 amp power. Think a fast charger for an Android or Bluetooth speaker. If you use a weaker power, the Operating System may not load up.

The default username and password for the unit is:
```bash
user: libre
password: potato
```

You'll want to change this immediately for increased security.

## Accessing Redis
The potato unit is preinstalled with Redis-Stack in a docker container. You can access Redis via the command-line on the Potato with the Terminal program: 

First start the Redis Docker container:

```bash
libre@librepotato:~ $ docker start redis-stack
```

Now connect via command-line:
```bash
libre@librepotato:~ $ redis-cli
127.0.0.1:6379>
````

You can also run RedisInsight, the Redis GUI that is included with Redis Stack.  Open the Chromium Browser and navigate to `localhost:8001`. 

### Connecting to Redis on your local network
You are able to connect to Redis hosted on the Potato via the local network. First find the IP address of the Potato:
```bash
libre@librepotato:~ $ hostname -I
192.168.1.12 172.17.0.1
```
Now, from a separate computer, you can connect via command-line:
```bash
$ redis-cli -h 192.168.1.12
192.168.1.12:6379>
```

Note that IP addresses will vary by computer and network status. You can also connect to Redis in your codebase with clients using the same IP address. You now have a developer instance of Redis hosted locally in your own cloud! Always remember that cloud computing is just someone else's computer 😉! 


## Optional setup

Here are some upgrades and improvements that are by no means necessary but do improve the development experience in our opinion. We have provided relevant links for further reading and expansion as you see fit.

### Update hostname
Since the Potato is using Raspbian Linux, the OS assumes you are running a Raspberry Pi. This can cause some confusion, as the hostname displayed by the unit will be `raspberrypi` on networks.  To change this, we recommend updating the hosthame to `librepotato` or some other name. [This](https://www.tomshardware.com/how-to/raspberry-pi-change-hostname) article explains how to update the hostname.

### Enable Redis at start
If you would like the Redis Stack [service](https://linux.how2shout.com/how-to-start-docker-container-automatically-on-boot-in-linux/) to start on the Potato on startup, further steps will need to be taken.

Create a service file within the systemd etc folder to start Redis at boot up:

```bash
libre@librepotato:~ $ sudo nano /etc/systemd/system/redis-server.service
```

Paste into redis-server.service:

```bash
[Unit]
Description=Redis Stack
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a redis-stack
ExecStop=/usr/bin/docker stop -t 10 redis-stack

[Install]
WantedBy=multi-user.target
```

To save and exit, press Ctrl+O, Enter, then Ctrl+X.

Enable the service file, start the redis-server.service unit, then test Redis functionality.

```bash
libre@librepotato:~ $ sudo systemctl enable redis-server.service
libre@librepotato:~ $ sudo systemctl start redis-server.service
libre@librepotato:~ $ redis-cli PING
"PONG"
```

Now when you reboot your Potato Redis will be up and running.

### Enable SSH
Enabling SSH allows you to connect to the Potato via network access. This will allow you to access and manipulate files on the Potato without the need of peripherals connected; you can connect and interact with it on your terminal on your usual device. [Here](https://linuxize.com/post/how-to-enable-ssh-on-raspberry-pi/) is an article outlining how to enable SSH.

