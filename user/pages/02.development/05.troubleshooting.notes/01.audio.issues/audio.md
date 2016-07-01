
# Audio errors and issues
If your questions and issues aren't addressed here please reach out to us via the support channels.

## Alsa
Alsa is the sound architecture on most linux systems but particularly on Ubuntu and Raspbian. Alsa comes with a series of tools used to select a usb audio device and configure them for use with your device.

Most of the Alsa configuration is done on the command line and below are some useful links and configuration files to help you setup and debug your system. These are by no means the end-all commands and tools and you will need to look up configuration files for your own distribution. There are many changes based on linux distribution and release so google is still your best friend.

### Useful audio cli commands and apps

 - aplay -l
    - command lind audio-player lists audio devices
 - aplay -L
    -  list all PCMs defined
 - lsusb
    - Should show plugged in usb devices. In particular your audio device.
    - Mine shows as C-media usb
 - pulseaudio -D
    - sound app to install `apt-get install pulseaudio`
    - the -D runs the daemon and needed to be running on my device for audio to work
    - puleaudio is run from the visual system and you should find it in your start menu
 - alsamixer
    - built in [sound mixer](http://www.linuxplanet.com/linuxplanet/tutorials/7134/1)
    - lets you select your device and get it running from command line.

### Raspbian and Ubuntu Jessie

Jessie has moved the alsa configuration files see below for the new files and locations. 

 - Alsa docs for a general sense: ![docs](http://alsa.opensrc.org/MultipleCards#alsa.conf_structure)
 - Config files
    - /usr/share/alsa/alsa.conf
    - ~/.asoundrc

#### Mycroft voice Alsa error: `ALSA lib confmisc.c:768:(parse_card) cannot find card '0'` on running mycroft.sh start

I get this error on initial install and running the setup of my mycroft-core on a raspberry pi 3 device prior to the Cerberus registration. The error shows that I had issues using my usb device and finding card 0. I could see my [kinobo usb 2.0 device but not use it.](https://www.amazon.com/Kinobo-Microphone-Desktop-Recognition-Software/dp/B00IR8R7WQ) While i'm setting this up for my own usb device this should help other devices and card #s. 

I found the fix in [this](http://raspberrypi.stackexchange.com/questions/37177/best-way-to-setup-usb-mic-as-system-default-on-raspbian-jessie) StackOverflow(SO) post.

Short version of the instructions I'm listing below:

 1. Figure out Card and Device # by running lsusb (See below).
 2. Run `sudo nano /usr/share/alsa/alsa.conf`
    1. Change numbers below to your card # (1 for me)
    2. `defaults.ctl.card 0` to `defaults.ctl.card yourcard#`
    3. `defaults.pcm.card 0` to `defaults.pcm.card yourcard#`
 4. Create and edit your personal alsa config file by running `sudo nano~/.asoundrc` and changing to your card #.

> pcm.!default  {
>     type hw
>     card 1 }
> 
> ctl.!default {
>     type hw
>     card 1 }

 5. Run `sudo reboot`
 6. You may need to run `pulseaudio -D` again prior to running the mycroft start script.
 7. run `./mycroft.sh start` from the instructions and that should now find the device and you should be able to get your cerberus registration code.

##### Getting your audio device and card #
Get lsusb readout (The C-Media is my usb audio device):

   $ lsusb
      Bus 001 Device 004: ID 0d8c:013c C-Media Electronics, Inc. CM108 Audio Controller
      Bus 001 Device 005: ID 0c40:8000
      Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter
      Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp.
      Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

Get a list of your audio cards with:

    $ cat /proc/asound/cards
    1 [Device         ]: USB-Audio - USB PnP Sound Device
                      C-Media Electronics Inc. USB PnP Sound Device at usb-3f980000.usb-1.4, full spe

Knowing the card # you can get the rest of the info by changing card1 to your card:

    $ cat /proc/asound/card1/pcm0c/info
       card: 1
       device: 0
       subdevice: 0
       stream: CAPTURE
       id: USB Audio
       name: USB Audio
       subname: subdevice #0
       class: 0
       subclass: 0
       subdevices_count: 1
       subdevices_avail: 1

### Raspbian and Ubuntu Wheezy

 - Config files
    - /etc/modprobe.d/alsa-base.conf

