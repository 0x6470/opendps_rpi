# Upgrade your DPS5005, credits to https://github.com/kanflo/opendps
> excuse my English

I firstly tried to use Johan Kanflo's tutorial to modify my DPS5005, but I stuck at the point, where I had to establish the OpenOCD connection. I got a "memory-access-error (core dumped)" error, which is why I tried the same on my Debian system (no VM), but this also failed. So spent roughly 4 hours to find a solution, but nothing helped me out. But now, after 6 days, I found a solution:
## Using the Raspberry Pi's GPIOs (works with 2 and 3)
## Building OpenOCD
I firstly installed the following components via apt:
```
sudo apt-get install git autoconf libtool make pkg-config libusb-1.0-0 libusb-1.0-0-dev
```
I was surprised, when I found out, that I could easily install gcc-arm-none-eabi, by typing
```
sudo apt-get install gcc-arm-none-eabi
```
Now we need to download OpenOCD.
```
sudo git clone git://git.code.sf.net/p/openocd/code /root/openocd
```
When done, we need to build it
```
cd /root/openocd
./bootstrap
./configure --enable-sysfsgpio --enable-bcm2835gpio
make
sudo make install
```
Move stm32f1x.cfg to /root/opendps/openocd/scripts/target and replace the existing file. (If OpenOCD Version is >0.10.0)

## Building OpenDPS (according to Johan's instructions)
```
git clone --recursive https://github.com/kanflo/opendps.git /root/opendps
cd /root/opendps
make -C libopencm3
make -C opendps
make -C dpsboot
```

## Making the configuration
Sadly Johan's instructions can't be used here.

Move the openocd.cfg (credits to: http://www.stm32duino.com/viewtopic.php?t=940#p10897) file provided into /root/opendps/openocd/scripts
and wire your DPS like shown here
<p align="center">
<img src="https://johan.kanflo.com/wp-content/uploads/2017/06/Screen-Shot-2017-07-29-at-01.30.02.png" alt="DPS5005's pins"/>
</p>
and described in the openocd.cfg file.

## Raspberry Pi 3 only
*Clone the raspberrypi2-native.cfg file in /root/opendps/openocd/scripts/interfaces to raspberrypi3-native.cfg and change*

```
bcm2835gpio_speed_coeffs 146203 36
```
*to*
```
bcm2835gpio_speed_coeffs 194938 48
```
*Change the content of openocd.cfg as shown in it.*


Then power the DPS.

```
cd openocd/scripts
openocd -f openocd.cfg
```
If you see "Info : stm32f1x.cpu: hardware has 6 breakpoints, 4 watchpoints", you made everything correct and can go on.
From this point on you can follow Johan's instructions again, starting at the point "telnet localhost 4444":
https://johan.kanflo.com/upgrading-your-dps5005/
