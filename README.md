# Redpesk CAN Low level binding's plugins

You will find more advanced plugins in the repository using the CAN low level
binding. This concern mainly ARS408 maritim Radar or handle the NMEA2000
protocol by example.

## How to build

### Pre-requisites

To build theses plugins you need the following development packages installed on
your system:

- rp-app-framework-binder
- rp-can-low-level
- rp-libappcontroller
- rp-libafb-helpers

And the build tools:

- cmake
- gcc or clang
- systemd-devel
- oniguruma
- json-c
- jq

#### From the sources

Get the Redpesk Application Framework Binder and install it:

```bash
$ git clone https://github.com/redpesk-core/app-framework-binder.git
Cloning into 'app-framework-binder'...
remote: Enumerating objects: 10708, done.
remote: Counting objects: 100% (10708/10708), done.
remote: Compressing objects: 100% (2908/2908), done.
remote: Total 10708 (delta 8036), reused 10389 (delta 7717), pack-reused 0
Receiving objects: 100% (10708/10708), 4.25 MiB | 681.00 KiB/s, done.
Resolving deltas: 100% (8036/8036), done.
$ mkdir build
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=DEBUG -DUSE_SIMULATION=YES ..
-- The C compiler identification is GNU 10.2.1
-- The CXX compiler identification is GNU 10.2.1
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PkgConfig: /usr/bin/pkg-config (found version "1.6.3")
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed
-- Looking for pthread_create in pthreads
-- Looking for pthread_create in pthreads - not found
-- Looking for pthread_create in pthread
-- Looking for pthread_create in pthread - found
-- Found Threads: TRUE
-- Checking for module 'json-c'
--   Found json-c, version 0.13.1
-- Looking for include file magic.h
-- Looking for include file magic.h - found
-- Looking for magic_load in magic
-- Looking for magic_load in magic - found
-- Checking for module 'libsystemd>=222'
--   Found libsystemd, version 245
-- Checking for module 'libmicrohttpd>=0.9.60'
--   Found libmicrohttpd, version 0.9.71
-- Checking for module 'openssl'
--   Found openssl, version 1.1.1g
-- Checking for module 'uuid'
--   Found uuid, version 2.35.2
-- Checking for module 'cynara-client'
--   Package 'cynara-client', required by 'virtual:world', not found
-- Checking for module 'check'
--   Found check, version 0.14.0
-- Configuring done
-- Generating done
-- Build files have been written to: /home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-core/app-framework-binder/build
$ make -j
Scanning dependencies of target afb-json2c
Scanning dependencies of target afb-genskel
Scanning dependencies of target afb-dbus-binding
Scanning dependencies of target hello3
Scanning dependencies of target hello2
Scanning dependencies of target demoContext
Scanning dependencies of target afb-lib
Scanning dependencies of target tic-tac-toe
Scanning dependencies of target afb-exprefs
Scanning dependencies of target afbwsc
Scanning dependencies of target authLogin
Scanning dependencies of target demoPost
Scanning dependencies of target tuto-app1
[  1%] Building C object src/devtools/CMakeFiles/afb-genskel.dir/genskel.c.o
[  2%] Building C object src/devtools/CMakeFiles/afb-json2c.dir/json2c.c.o
[  3%] Building C object src/devtools/CMakeFiles/afb-exprefs.dir/exprefs.c.o
[  4%] Building C object bindings/intrinsics/CMakeFiles/afb-dbus-binding.dir/afb-dbus-binding.c.o
[  5%] Building C object bindings/samples/CMakeFiles/hello3.dir/hello3.c.o
[  6%] Building C object bindings/samples/CMakeFiles/hello2.dir/hello2.c.o
[  7%] Building C object src/CMakeFiles/afbwsc.dir/afb-ws.c.o
[  9%] Building C object bindings/samples/CMakeFiles/tic-tac-toe.dir/tic-tac-toe.c.o
[  9%] Building C object src/CMakeFiles/afbwsc.dir/afb-ws-client.c.o
[ 10%] Building C object bindings/samples/CMakeFiles/demoPost.dir/DemoPost.c.o
[...]
[ 94%] Linking C executable test-globset
[ 95%] Linking C executable test-session
[ 96%] Linking C executable test-apiv3
[ 96%] Built target test-globset
[ 97%] Linking C executable test-u16id
[ 97%] Built target test-u16id
[ 97%] Built target test-session
[ 98%] Linking C executable test-wrap-json
[ 98%] Built target test-apiv3
[ 99%] Linking C executable afb-daemon
[ 99%] Built target test-wrap-json
[100%] Linking C executable test-apiset
[100%] Built target test-apiset
[100%] Built target afb-daemon
$ sudo make install
```

Do the same for the lib afb-helpers and appcontroller:

```bash
git clone https://github.com/redpesk-common/libafb-helpers.git
git clone https://github.com/redpesk-common/libappcontroller.git
# install afb-helpers
cd libafb-helpers
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG ..
make -j
sudo make install
# install appcontroller
cd ../libappcontroller
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG ..
make -j
sudo make install
```

Finally, get the CAN low level binding and install it:

```bash
J1939_PRESENT=$([ "$(find /lib/modules/$(uname -r) -name "can-j1939.ko*")" ] && echo ON || echo OFF)
git clone https://github.com/redpesk-common/rp-can-low-level.git
cd rp-can-low-level
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG -DWITH_FEATURE_J1939=${J1939_PRESENT} -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j
sudo make install
```

#### From the distribution packages

***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***
***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***
***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***

Install the can-low-level binding from official packages repositories if you
want to test your **CAN low level**'s plugin natively on your host:

^- Fedora

```bash
export DISTRO="Fedora_32"
export REVISION=28
source /etc/os-release ; export DISTRO="${NAME}_${VERSION_ID}"
sudo wget -O /etc/yum.repos.d/redpesk_devel_${REVISION}.repo http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/redpesk_devel_${REVISION}.repo
sudo dnf install rp-can-low-level
```

^- OpenSUSE

```bash
export DISTRO="openSUSE_Leap_15.2"
export REVISION=28
source /etc/os-release; export DISTRO=$(echo $PRETTY_NAME | sed "s/ /_/g")
sudo zypper ar http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/redpesk_devel_${REVISION}.repo
sudo zypper --gpg-auto-import-keys ref
sudo zypper install rp-can-low-level
```

^- Ubuntu

```bash
export REVISION="28"
export DISTRO="xUbuntu_20.04"
wget -O - http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/Release.key | sudo apt-key add -
sudo bash -c "cat >> /etc/apt/sources.list.d/redpesk.list <<EOF
deb http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/ ./
EOF"
sudo apt-get update
sudo apt-get install rp-can-low-level
```

^- Debian

```bash
export REVISION="28"
export DISTRO="Debian_10"
wget -O - http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/Release.key | sudo apt-key add -
sudo bash -c "cat >> /etc/apt/sources.list.d/redpesk.list <<EOF
deb http://download.opensuse.org/repositories/IotBzh/redpesk_devel_${REVISION}/${DISTRO}/ ./
EOF"
sudo apt-get update
sudo apt-get install rp-can-low-level
```

***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***
***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***
***INSTRUCTIONS WHEN THE PUBLICS OBS PROJECTS AND REPOS WILL BE CREATED***

### Build

This project do not build anything by default. You have to select the plugin
you want to build:

```bash
$ mkdir build
$ cd build
$ cmake ..
-- The C compiler identification is GNU 10.2.1
-- The CXX compiler identification is GNU 10.2.1
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
=== Including plugin 'ARS408_signals'
=== Including plugin 'can-ethernet'
=== Including plugin 'gps-signals'
=== Including plugin 'j1939-signals'
=== Including plugin 'joystick-signals'
=== Including plugin 'simrad-draft-signals'
=== Including plugin 'n2k-basic-signals'
-- Configuring done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/claneys/Workspace/Sources/IOTbzh/github/redpesk/redpesk-can-low-level-plugins/build
$ make gps-signals
Scanning dependencies of target gps-signals
[ 50%] Building CXX object src/gps/CMakeFiles/gps-signals.dir/gps-signals.cpp.o
[100%] Linking CXX shared module gps-signals.ctlso
[100%] Built target gps-signals
```

## How to use


### Adapt the CAN low level configuration file

Change the `control-rp-can-low-level.json` file by selecting the plugin(s) to
load. Here is an example selecting the `gps-signals` plugin:

```json
{
        "$schema": "",
        "metadata": {
                "uid": "Low Can",
                "version": "1.0",
                "api": "low-can",
                "info": "Low can Configuration"
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
one. Also make it correspond to your needs against the *json* configuration you
used. Here about the gps-signals json definitions, we only need the **hs** bus.

### Setup CAN bus

#### Virtual CAN device

Connected to the target, here is how to load the virtual CAN device driver and set up a new vcan device :

```bash
modprobe vcan
ip link add vcan0 type vcan
ip link set vcan0 up
You also can named your linux CAN device like you want and if you need name it can0 :
```

```bash
modprobe vcan
ip link add can0 type vcan
ip link set can0 up
```

#### CAN device using the USB CAN adapter

Using real connection to CAN bus of your car using the USB CAN adapter connected to the OBD2 connector.

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

On a Rcar Gen3 board, you’ll have your CAN device as can1 because `can0` already
exists as an embedded device.

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

#### Rename an existing CAN device

You can rename an existing CAN device using following command and doing so move
an existing `can0` device to anything else and then use another device as `can0`
. For a Rcar Gen3 board do the following by example:

```bash
sudo ip link set can0 down
sudo ip link set can0 name bsp-can0
sudo ip link set bsp-can0 up
```

Then connect your USB CAN device that will be named `can0` by default.

### Install and launch the binding

Now, install the configuration and plugin(s) to be used by the binding:

```bash
$ cd build
$ sudo make install
Install the project...
-- Install configuration: ""
-- Up-to-date: /usr/local/rp-can-low-level/etc/control-rp-can-low-level.json
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
$ afb-daemon --name=afbd-rp-low-can-level --port=1234 --roothttp=. --tracereq=common --token= --workdir=/usr/local/rp-can-low-level --binding=lib/afb-low-can-binding.so --ws-server=unix:/tmp/low-can -vvv
------BEGIN OF CONFIG-----
-- {
--    "name": "afbd-rp-low-can-level",
--    "port": 1234,
--    "roothttp": ".",
--    "tracereq": "common",
--    "token": "",
--    "workdir": "/usr/local/rp-can-low-level",
--    "binding": [
--      "lib/afb-low-can-binding.so"
--    ],
--    "ws-server": [
--      "unix:/tmp/low-can"
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
INFO: binding [lib/afb-low-can-binding.so] looks like an AFB binding V3
INFO: API low-can added
WARNING: [API low-can] CTL-INIT JSON file found but not used : /usr/local/rp-can-low-level/etc/control-rp-can-low-level.json [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-config.c:93,ConfigSearch]
WARNING: [API low-can] CTL-INIT JSON file found but not used : /home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/rp-can-low-level/build/package/etc/control-rp-can-low-level.json [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-config.c:93,ConfigSearch]
INFO: [API low-can] CTL-LOAD-CONFIG: loading config filepath=/usr/local/rp-can-low-level/etc/control-rp-can-low-level.json
DEBUG: [API low-can] Config { "active_message_set": 0, "dev-mapping": { "hs": "can0" }, "diagnostic_bus": "hs" }
DEBUG: [API low-can] BCM socket ifr_name is : can0
DEBUG: [API low-can] Shims initialized
DEBUG: [API low-can] Clearing existing diagnostic requests
DEBUG: [API low-can] Diagnostic Manager initialized
DEBUG: [API low-can] Plugin search path : '/usr/local/rp-can-low-level:/usr/local/rp-can-low-level/lib/..'
NOTICE: [API low-can] CTL-PLUGIN-LOADONE gps successfully registered
WARNING: [API low-can] Plugin multiple instances in searchpath will use /usr/local/rp-can-low-level/lib/plugins/gps-signals.ctlso [/home/claneys/Workspace/Sources/IOTbzh/gitlab/redpesk-common/libappcontroller/ctl-lib/ctl-plugin.c:247,LoadFoundPlugins]
INFO: Scanning dir=[/usr/local/lib64/afb] for bindings
INFO: binding [/usr/local/lib64/afb/afb-dbus-binding.so] isn't an AFB binding
DEBUG: Init config done
NOTICE: API low-can starting...
DEBUG: [API low-can] Found 0 signal(s)
INFO: API low-can started
NOTICE: API monitor starting...
INFO: API monitor started
WARNING: unable to set the upload directory . [/home/claneys/Workspace/Sources/IOTbzh/github/redpesk-core/app-framework-binder/src/main-afb-daemon.c:373,start_http_server]
NOTICE: Serving rootdir=. uploaddir=/tmp
NOTICE: Listening interface *:1234
NOTICE: Browser URL= http://localhost:1234
```

### Simple usage examples

Plug your GPS to your hardware CAN bus or use `canplayer` to replay a set of CAN
messages to send, ie:

```bash
canplayer gps-record
```

Then connect to your binding using the CLI utility provided with the `binder`
and call directly API's verbs, ie:

```bash
$ afb-client-demo -H -d unix:/tmp/low-can
list
ON-REPLY 1:list: success
[
  "messages.gps.simulateur.bearing",
  "messages.gps.simulateur.latitude",
  "messages.gps.simulateur.longitude",
  "messages.gps.simulateur.speed",
  "messages.gps.attack.bearing",
  "messages.gps.attack.latitude",
  "messages.gps.attack.longitude",
  "messages.gps.attack.speed",
  "messages.gps.accelero.bearing",
  "messages.gps.accelero.latitude",
  "messages.gps.accelero.longitude",
  "messages.gps.accelero.speed"
]
subscribe {"event": "gps*"}
ON-EVENT-CREATE: [1:low-can/messages.gps.simulateur.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [1]
ON-EVENT-CREATE: [2:low-can/messages.gps.simulateur.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [2]
ON-EVENT-CREATE: [3:low-can/messages.gps.simulateur.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [3]
ON-EVENT-CREATE: [4:low-can/messages.gps.simulateur.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [4]
ON-EVENT-CREATE: [5:low-can/messages.gps.attack.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [5]
ON-EVENT-CREATE: [6:low-can/messages.gps.attack.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [6]
ON-EVENT-CREATE: [7:low-can/messages.gps.attack.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [7]
ON-EVENT-CREATE: [8:low-can/messages.gps.attack.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [8]
ON-EVENT-CREATE: [9:low-can/messages.gps.accelero.bearing]
ON-EVENT-SUBSCRIBE 3:subscribe: [9]
ON-EVENT-CREATE: [10:low-can/messages.gps.accelero.latitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [10]
ON-EVENT-CREATE: [11:low-can/messages.gps.accelero.longitude]
ON-EVENT-SUBSCRIBE 3:subscribe: [11]
ON-EVENT-CREATE: [12:low-can/messages.gps.accelero.speed]
ON-EVENT-SUBSCRIBE 3:subscribe: [12]
ON-REPLY 3:subscribe: success
null
ON-EVENT-PUSH: [8]
{
  "id":292,
  "name":"messages.gps.attack.speed",
  "value":0.0,
  "timestamp":1601302590185122
}
ON-EVENT-PUSH: [5]
{
  "id":292,
  "name":"messages.gps.attack.bearing",
  "value":0.0,
  "timestamp":1601302590185226
}
ON-EVENT-PUSH: [6]
{
  "id":292,
  "name":"messages.gps.attack.latitude",
  "value":329.42880249023438,
  "timestamp":1601302590185337
}
ON-EVENT-PUSH: [7]
{
  "id":292,
  "name":"messages.gps.attack.longitude",
  "value":239.43028259277344,
  "timestamp":1601302590185392
}
get { "event": "gps.attack.longitude" }
ON-REPLY 4:get: success
[
  {
    "event":"messages.gps.attack.longitude",
    "value":239.43028259277344
  }
]
```

Get more details from the binding complete [documentation](https://docs.iot.bzh/docs/en/master/apis_services/reference/signaling/5-Usage.html)