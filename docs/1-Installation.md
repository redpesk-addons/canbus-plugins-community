# How to install CANbus plugins

## Install from the distribution packages

To set up your build host please follow [this section](../../developer-guides/host-configuration/docs/1-Setup-your-build-host.html).

Then you could install the plugins using the following commands:

### Ubuntu/Debian

```bash
# Pick the one you want.
sudo apt update
sudo apt install canbus-plugins-engine
sudo apt install canbus-plugins-hvac
sudo apt install canbus-plugins-obd2
sudo apt install canbus-plugins-vcar
```

### Fedora

```bash
# Pick the one you want.
sudo dnf install canbus-plugins-engine
sudo dnf install canbus-plugins-hvac
sudo dnf install canbus-plugins-obd2
sudo dnf install canbus-plugins-vcar
```

### OpenSuse

```bash
# Pick the one you want.
sudo zypper install canbus-plugins-engine
sudo zypper install canbus-plugins-hvac
sudo zypper install canbus-plugins-obd2
sudo zypper install canbus-plugins-vcar
```

## Build and install from the sources

### Pre-requisites

To build theses plugins you need the following development packages installed on
your system:

- afb-binding-devel
- canbus-binding-devel
- afb-libcontroller
- afb-libhelpers

And the building tools:

- cmake
- gcc or clang
- systemd-devel
- oniguruma
- json-c
- jq

#### From the distribution packages

Install the canbus-binding from official packages repositories if you
want to test your **canbus-plugin** natively on your host:

^- Fedora

```bash
export DISTRO="Fedora_33"
export REVISION=33
source /etc/os-release ; export DISTRO="${NAME}_${VERSION_ID}"
sudo wget -O /etc/yum.repos.d/redpesk_devel_${REVISION}.repo https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/redpesk-sdk_fedora.repo
sudo dnf install canbus-binding
```

^- OpenSUSE

```bash
export DISTRO="openSUSE_Leap_15.2"
export REVISION=33
source /etc/os-release; export DISTRO=$(echo $PRETTY_NAME | sed "s/ /_/g")
sudo zypper ar https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/redpesk-sdk_suse.repo
sudo zypper --gpg-auto-import-keys ref
sudo zypper install canbus-binding
```

^- Ubuntu

```bash
export DISTRO="xUbuntu_20.04"
export REVISION="33"
wget -O - https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/${DISTRO}/Release.key | sudo apt-key add -
sudo bash -c "cat >> /etc/apt/sources.list.d/redpesk.list <<EOF
deb https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/${DISTRO}/ ./
EOF"
sudo apt-get update
sudo apt-get install canbus-binding
```

^- Debian

```bash
export DISTRO="Debian_10"
export REVISION="33"
wget -O - https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/${DISTRO}/Release.key | sudo apt-key add -
sudo bash -c "cat >> /etc/apt/sources.list.d/redpesk.list <<EOF
deb https://primary-mirror.redpesk.iot/redpesk-devel/releases/${REVISION}/sdk/${DISTRO}/ ./
EOF"
sudo apt-get update
sudo apt-get install canbus-binding
```

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

Do the same for the lib afb-libhelpers and afb-libcontroller:

```bash
git clone https://github.com/redpesk-common/afb-libhelpers.git
git clone https://github.com/redpesk-common/afb-libcontroller.git
# install afb-libhelpers
cd afb-libhelpers
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG ..
make -j
sudo make install
# install afb-libcontroller
cd ../afb-libcontroller
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG ..
make -j
sudo make install
```

Finally, get the canbus-binding and install it:

```bash
git clone https://github.com/redpesk-common/canbus-binding.git
cd canbus-binding
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=DEBUG  -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j
sudo make install
```

You can specify if you want the j1939 and isotp enabled by add
these options to you cmake command:

-DWITH_FEATURE_J1939=ON/OFF
and
-DWITH_FEATURE_ISOTP=ON/OFF

### Build

This project does not build anything by default. You have to select the plugin
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
-- Build files have been written to: /home/claneys/Workspace/Sources/IOTbzh/github/redpesk/canbus-plugins-redpesk/build
$ make gps-signals
Scanning dependencies of target gps-signals
[ 50%] Building CXX object src/gps/CMakeFiles/gps-signals.dir/gps-signals.cpp.o
[100%] Linking CXX shared module gps-signals.ctlso
[100%] Built target gps-signals
```
