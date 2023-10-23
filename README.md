<div align="left">

![GitHub Actions](https://github.com/stephendade/Rpanion-server/actions/workflows/node.js.yml/badge.svg)
[![Coverage Status](https://coveralls.io/repos/github/stephendade/Rpanion-server/badge.svg)](https://coveralls.io/github/stephendade/Rpanion-server)

</div>

User Documentation is at https://www.docs.rpanion.com/software/rpanion-server

<p float="left">
<img src="https://raw.githubusercontent.com/stephendade/Rpanion-server/master/images/controller.png" width="200">
<img src="https://raw.githubusercontent.com/stephendade/Rpanion-server/master/images/network.png" width="200">
<img src="https://raw.githubusercontent.com/stephendade/Rpanion-server/master/images/video.png" width="200">
</p>

# Rpanion-server

This is a node.js based server for companion computers used in Mavlink-based vehicles (ie Ardupilot, PX4).

It presents a web-based interface (running on the companion computer), where system settings such as network,
telemetry and video streaming can be configured from.

On the Raspberry Pi, Rpanion-server is compatible with the Raspberry Pi OS and Ubuntu 20.04 LTS.

On the Nvidia Jetson, Rpanion-server is compatible with Ubuntu 18.04 LTS.

On the [Libre Computer Le Potato](https://libre.computer/products/aml-s905x-cc/), Rpanion-server is compatible with their flavor of [Raspberry Pi OS](https://distro.libre.computer/ci/raspbian/).

## Features

Rpanion-server allows the user to configure:

- Flight Controller telemetry routing to udp outputs
- Video Streaming via an RTSP server
- Network configuration
- NTRIP Streaming
- Logging (tlog and bin logs)

## Dependencies and First-time configuration

The following instructions assumes you have cloned the Rpanion-server repository to ``~/``.
If not, use:

```
cd ~/ && git clone --recursive https://github.com/hilarioaraujo/Rpanion-server.git
```

### Automatic (Raspberry Pi)

If running Ubuntu 22.04 OS on the Pi, use:

```
cd ~/Rpanion-server/deploy/ && sudo bash ./RasPi-ubuntu22-deploy.sh
```

Note the CSI camera does not currently work on Ubuntu 22.04, due to incompatibilities with the Raspberry Pi.


If not already configured for an initial Wifi hotspot, run the ``./deploy/wifi_access_point.sh`` script.
The hotspot has the SSID "rpanion" and password "rpanion123". The Pi's IP address will be 10.0.2.100,
so the Rpanion-sever website will be available at http://10.0.2.100:3000.


### Automatic (Nvidia Jetson)

For the Nvidia Jetson run the below command on a fresh OS install
to configure and install Rpanion-server and all required dependencies. Note this does not
configure an initial Wifi hotspot.

```
cd ./Rpanion-server/deploy && ./jetson-deploy.sh
```

### Automatic (x86 boards and laptops like Latte Panda)

For any x86 based modules or laptops, run the following script
to configure and install Rpanion-server and all required dependencies. Note this does not
configure an initial Wifi hotspot.

```
cd ./Rpanion-server/deploy && ./x86-ubuntu20-deploy.sh
```

## After installation

If using a usb to serial converter, you might need to modify permissions of the device.

**Temporary device permission update**

```
sudo chmod 666 /dev/ttyACM0
```

**Persistent device permission update**

Follow the steps on this site for your specific device:
[https://www.xmodulo.com/change-usb-device-permission-linux.html](https://www.baeldung.com/linux/serial-ports-without-root)

```
sudo usermod -a -G dialout ceov
```

## Do the checkout Tests

Unit tests are split into separate commands for the frontend (ReactJS) and backend.

Unit tests can be run with the command:

```bash
npm run testback
npm run testfront
```
## Building and Running in production mode

Running in production mode builds the reactJS app first. This gives
performance increases over running in development mode.

```bash
npm run build
PORT=3000
npm run server
```

## Building and Running in development mode

Running in development mode allows for any code changes to trigger a restart of Rpanion-server. 

Rpanion-server consists of a node.js server running on port 3001 and a React frontend application
running on port 3000 in development mode. 

In production mode, the React application is rendered statically
from the node.js server on port 3001. This can be overidden via setting the ``PORT`` environment
variable (see ``rpanion.service`` for for example).

You can start the server on its own with the command:

```bash
npm run server
```

Run the React application on its own with the command:

```bash
npm start
```

Run both applications together with the command:

```bash
npm run dev
```

At this point, the website will be active at ``http://<ip of device>:3000``


### Updating

When updating Rpanion-server from Github, run ``npm install`` to grab any
changed dependencies.

If running in production mode, run ``npm run build`` too for the ReactJS app 
to be rebuilt.

If running Rpanion-server as a service, ensure to restart the service.

An automatic update script performed via:

```
./Rpanion-server/deploy/upgrade.sh
```

## Releasing

Use ``npx npm-check-updates -u`` to update nodejs libraries.

Use ``npm version minor`` to create a new release.

To produce a disk image from a SD card, insert the card and run ``./deploy/create_image.sh``.


## Running as a Service

To have Rpanion-server running on automatically on boot, there is an included systemd service file.

This can be enabled via:

```
sudo cp rpanion.service /etc/systemd/system
sudo systemctl enable rpanion.service
```


## Change Ethernet IP in linux:

sudo nano /etc/netplan/*.yaml

https://linuxhint.com/change-ip-address-ubuntu/


 ethernets:
      eth0:
        addresses: [192.168.88.3/24]
        gateway4: 192.168.88.1
        nameservers:
                addresses: [8.8.4.4,8.8.8.8] 