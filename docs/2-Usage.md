# How to use

## Adapt the canbus-binding configuration file

Change the `control-canbus-binding.json` file by selecting the plugin(s) to
load. Here is an example selecting the `gps-signals` plugin:

```json
{
        "$schema": "",
        "metadata": {
                "uid": "CAN bus",
                "version": "2.0",
                "api": "canbus",
                "info": "CAN bus Configuration"
        },
        "config": {
                "active_message_set": 0,
                "dev-mapping": {
                        "hs": "can0"
                },
                "diagnostic_bus": "hs"
        },
        "plugins": [
                {
                        "uid": "gps-signals",
                        "info": "gps signals",
                        "libs": "gps-signals.ctlso"
                }
        ]
}
```

You also have to change the CAN *device* if you do not use the *can0* default
one. Also, make it correspond to your needs against the *json* configuration you
used. Here for the gps-signals json definitions, we only need the **hs** bus.

## Setup CAN bus

### Virtual CAN device

Connected to the target, here is how to load the virtual CAN device driver and set up a new vcan device :

```bash
modprobe vcan
ip link add vcan0 type vcan
ip link set vcan0 up
```

You can also call your linux CAN device as you like, for example if you need to name it can0 :

```bash
modprobe vcan
ip link add can0 type vcan
ip link set can0 up
```

### CAN device using the USB CAN adapter

Use the real connection to CAN bus of your device using an USB CAN adapter.

Once connected, launch dmesg command and search which device to use:

```bash
$ dmesg
[...]
[  131.871441] usb 1-3: new full-speed USB device number 4 using ohci-pci
[  161.860504] can: controller area network core (rev 20120528 abi 9)
[  161.860522] NET: Registered protocol family 29
[  177.561620] usb 1-3: USB disconnect, device number 4
[  191.061423] usb 1-2: USB disconnect, device number 3
[  196.095325] usb 1-2: new full-speed USB device number 5 using ohci-pci
[  327.568882] usb 1-2: USB disconnect, device number 5
[  428.594177] CAN device driver interface
[ 1872.551543] usb 1-2: new full-speed USB device number 6 using ohci-pci
[ 1872.809302] usb_8dev 1-2:1.0 can0: firmware: 1.7, hardware: 1.0
[ 1872.809356] usbcore: registered new interface driver usb_8dev
```

Here device is named `can0`.

This instruction assuming a speed of 500000kbps for your CAN bus, you can try
others supported bitrate like 125000, 250000 if 500000 doesn’t work:

```bash
$ ip link set can0 type can bitrate 500000
$ ip link set can0 up
$ ip link show can0
  can0: <NOARP, UP, LOWER_UP, ECHO> mtu 16 qdisc pfifo_fast state UNKNOWN qlen 10
    link/can
    can state ERROR-ACTIVE (berr-counter tx 0 rx 0) restart-ms 0
    bitrate 500000 sample-point 0.875
    tq 125 prop-seg 6 phase-seg1 7 phase-seg2 2 sjw 1
    sja1000: tseg1 1..16 tseg2 1..8 sjw 1..4 brp 1..64 brp-inc 1
    clock 16000000
```

On a Rcar Gen3 board for example, you’ll have your CAN device as can1 because `can0` already exists as an embedded device.

The instructions will be the same:

```bash
$ ip link set can1 type can bitrate 500000
$ ip link set can1 up
$ ip link show can1
  can0: <NOARP, UP, LOWER_UP, ECHO> mtu 16 qdisc pfifo_fast state UNKNOWN qlen 10
    link/can
    can state ERROR-ACTIVE (berr-counter tx 0 rx 0) restart-ms 0
    bitrate 500000 sample-point 0.875
    tq 125 prop-seg 6 phase-seg1 7 phase-seg2 2 sjw 1
    sja1000: tseg1 1..16 tseg2 1..8 sjw 1..4 brp 1..64 brp-inc 1
    clock 1600000
```

### Rename an existing CAN device

You can rename an existing CAN device using following command and thus move
an existing `can0` device to anything else. You will then be able to use
another device as `can0`. For example, using a Rcar Gen3 board,
do the following :

```bash
sudo ip link set can0 down
sudo ip link set can0 name bsp-can0
sudo ip link set bsp-can0 up
```

Then connect your USB CAN device which will be named `can0` by default.

## Install and launch the binding

Now, install the configuration and plugin(s) to be used by the binding:

```bash
$ cd build
$ sudo make install
Install the project...
-- Install configuration: ""
-- Up-to-date: /usr/local/canbus-binding/etc/control-canbus-binding.json
$ make gps-signal
Scanning dependencies of target gps-signals
[ 50%] Building CXX object src/gps/CMakeFiles/gps-signals.dir/gps-signals.cpp.o
[100%] Linking CXX shared module gps-signals.ctlso
[100%] Built target gps-signals
$ sudo make install_gps-signal
Scanning dependencies of target install_gps-signals
[100%] Built target install_gps-signals
```

Launch it using the `afb-daemon` (look out at the gps-signals.ctlso plugin load):

```bash
$ afb-daemon --name=afbd-canbus-binding --port=1234 --roothttp=. --tracereq=common --token= --workdir=/var/local/lib/afm/applications/canbus-binding --binding=lib/afb-canbus-binding.so --ws-server=unix:/tmp/canbus -vvv
------BEGIN OF CONFIG-----
-- {
--    "name": "afbd-canbus-binding",
--    "port": 1234,
--    "roothttp": ".",
--    "tracereq": "common",
--    "token": "",
--    "workdir": "/usr/local/canbus-binding",
--    "binding": [
--      "lib/afb-canbus-binding.so"
--    ],
--    "ws-server": [
--      "unix:/tmp/canbus"
--    ],
--    "apitimeout": 20,
--    "cache-eol": 100000,
--    "cntxtimeout": 32000000,
--    "session-max": 200,
--    "rootdir": ".",
--    "uploaddir": ".",
--    "rootbase": "/opa",
--    "rootapi": "/api",
--    "ldpaths": [
--      "/usr/local/lib64/afb"
--    ]
--  }
------END OF CONFIG-----
INFO: running with pid 34440
INFO: API monitor added
INFO: binding [lib/afb-canbus-binding.so] looks like an AFB binding V3
INFO: API canbus added
WARNING: [API canbus] CTL-INIT JSON file found but not used : /usr/local/canbus-binding/etc/control-canbus-binding.json [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-config.c:93,ConfigSearch]
WARNING: [API canbus] CTL-INIT JSON file found but not used : /home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/canbus-binding/build/package/etc/control-canbus-binding.json [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-config.c:93,ConfigSearch]
INFO: [API canbus] CTL-LOAD-CONFIG: loading config filepath=/usr/local/canbus-binding/etc/control-canbus-binding.json
DEBUG: [API canbus] Config { "active_message_set": 0, "dev-mapping": { "hs": "can0" }, "diagnostic_bus": "hs" }
DEBUG: [API canbus] BCM socket ifr_name is : can0
DEBUG: [API canbus] Shims initialized
DEBUG: [API canbus] Clearing existing diagnostic requests
DEBUG: [API canbus] Diagnostic Manager initialized
DEBUG: [API canbus] Plugin search path : '/usr/local/canbus-binding:/usr/local/canbus-binding/lib/..'
NOTICE: [API canbus] CTL-PLUGIN-LOADONE virtual-car successfully registered
WARNING: [API canbus] Plugin multiple instances in searchpath will use /usr/local/canbus-binding/lib/plugins/vcar-signals.ctlso [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-plugin.c:247,LoadFoundPlugins]
INFO: Scanning dir=[/usr/local/lib64/afb] for bindings
INFO: binding [/usr/local/lib64/afb/afb-dbus-binding.so] isn't an AFB binding
DEBUG: Init config done
NOTICE: API canbus starting...
DEBUG: [API canbus] Found 0 signal(s)
INFO: API canbus started
NOTICE: API monitor starting...
INFO: API monitor started
WARNING: unable to set the upload directory . [/home/claneys/Workspace/Sources/IOTbzh/github/redpesk-core/app-framework-binder/src/main-afb-daemon.c:373,start_http_server]
NOTICE: Serving rootdir=. uploaddir=/tmp
NOTICE: Listening interface *:1234
NOTICE: Browser URL= http://localhost:1234
```

## Simple usage examples

Plug your GPS to your hardware CAN bus or use `canplayer` to replay a set of CAN
messages to send, ie:

```bash
canplayer -I gps-record
```

Then connect to your binding using the CLI utility provided with the `binder`
and call directly API's verbs, ie:

```bash
$ afb-client-demo -H -d unix:/tmp/canbus
list
ON-REPLY 1:list: success
[
  "gps/simulateur.bearing",
  "gps/simulateur.latitude",
  "gps/simulateur.longitude",
  "gps/simulateur.speed",
  "gps/attack.bearing",
  "gps/attack.latitude",
  "gps/attack.longitude",
  "gps/attack.speed",
  "gps/accelero.bearing",
  "gps/accelero.latitude",
  "gps/accelero.longitude",
  "gps/accelero.speed"
]
gps/simulateur.bearing subscribe
ON-EVENT-CREATE: [1:low-can/gps/simulateur.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [1]
gps/simulateur.latitude subscribe
ON-EVENT-CREATE: [2:low-can/gps/simulateur.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [2]
gps/simulateur.longitude subscribe
ON-EVENT-CREATE: [3:low-can/gps/simulateur.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [3]
gps/simulateur.speed subscribe
ON-EVENT-CREATE: [4:low-can/gps/simulateur.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [4]
gps/attack.bearing subscribe
ON-EVENT-CREATE: [5:low-can/gps/attack.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [5]
gps/attack.latitude subscribe
ON-EVENT-CREATE: [6:low-can/gps/attack.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [6]
gps/attack.longitude subscribe
ON-EVENT-CREATE: [7:low-can/gps/attack.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [7]
gps/attack.speed subscribe
ON-EVENT-CREATE: [8:low-can/gps/attack.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [8]
gps/accelero.bearing subscribe
ON-EVENT-CREATE: [9:low-can/gps/accelero.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [9]
gps/accelero.latitude subscribe
ON-EVENT-CREATE: [10:low-can/gps/accelero.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [10]
gps/accelero.longitude subscribe
ON-EVENT-CREATE: [11:low-can/gps/accelero.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [11]
gps/accelero.speed subscribe
ON-EVENT-CREATE: [12:low-can/gps/accelero.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [12]
ON-REPLY 3:subscribe: success
null
ON-EVENT-PUSH: [8]
{
  "id":292,
  "name":"gps/attack.speed",
  "value":0.0,
  "timestamp":1601302590185122
}
ON-EVENT-PUSH: [5]
{
  "id":292,
  "name":"gps/attack.bearing",
  "value":0.0,
  "timestamp":1601302590185226
}
ON-EVENT-PUSH: [6]
{
  "id":292,
  "name":"gps/attack.latitude",
  "value":329.42880249023438,
  "timestamp":1601302590185337
}
ON-EVENT-PUSH: [7]
{
  "id":292,
  "name":"gps/attack.longitude",
  "value":239.43028259277344,
  "timestamp":1601302590185392
}
get { "event": "gps.attack.longitude" }
ON-REPLY 4:get: success
[
  {
    "event":"gps/attack.longitude",
    "value":239.43028259277344
  }
]
```

Get more details from the binding complete [documentation](https://docs.redpesk.bzh/docs/en/master/apis_services/reference/signaling/5-Usage.html)
