# TVBIRR

**TV Box Internet Radio Recorder (TVBIRR)** is an ansible playbook to configure a X96 Max+ (or similar, if you adjust the playbook) TV box to act as an internet radio streaming and recording device that you can operate entirely with the infrared remote included with the TV box. It includes a playlist of over 32000 free-to-play internet radio stations.

**TVBIRR** uses [music on console (moc)](http://moc.daper.net/) to stream channels and playlists, [keybinder](https://github.com/elopez/keybinder.git) to convert the infrared remote keypresses into commands, [JACK](https://jackaudio.org/) for audio playback and streams are recorded to MP3 files using [jack_capture](https://github.com/kmatheussen/jack_capture). You can see when **jack_capture** is recording the currently played station because CARD is displayed on the TV box LED display. You can easily customise it to play playlists of locally stored music too.

## Requirements

You need two things to run **TVBIRR**:

An **X96 Max Plus TV box**. At present **TVBIRR** only fully supports the X96 Max+ TV box but you could run it with all of its features, with a little tweaking of its scripts, on any on the TV boxes that have a display thats compatible with one of the [armbian-led preset files](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/armbian-docs/led_screen_display_control.md). I'd be willing to accept patches to add support for other TV boxes.

A Linux compatible USB to audio jack adapter, USB soundcard or USB speaker. There are two reasons for this. The onboard audio on the X96 Max pLus only works under Android, not Armbian Linux and its pretty poor quality anyway. A cheap £2 USB headphone jack adapter (I bought 2 for £3) will be much better quality than the onboard audio jack of the X96 Max+.

## Installation

Before you can install or use TVBIRR you must first install [Armbian for Amlogic TV Boxes](https://github.com/ophub/amlogic-s9xxx-armbian) aka s9xxx Armbian. You should be able to install TVBIRR under a Ubuntu or Debian based build of s9xxx Armbian. If you have the X96 Max Plus then you want to download a S905X3 build of Armbian.

After downloading the latest Armbian s905X3 image, you'll have to uncompress it with either `gunzip` or 7zip if you're on Windows and then burn it onto a MicroSD card or USB disk using BalenaEtcher. Note that you will have to enable multi-boot mode by sticking a pin into the AV port of you TV box when turning it on with the Armbian SD/USB inserted to that it will boot from it. This only needs to be done the first time you want to boot an SD card or USB drive, not every time.

OK so you've finally got Armbian running on your TV box! Now you can install TVBIRR by running, as root:

```
apt update
apt upgrade
apt install ansible
git clone https://github.com/danboid/TVBIRR.git
cd TVBIRR
ansible-playbook TVBIRR.yaml
```

After the ansible-playbook has run, installed and configured the various coponenents of TVBIRR, reboot your TV box with a USB audio device attached and you can start using TVBIRR with the remote.

