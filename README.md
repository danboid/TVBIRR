# TVBIRR

**TV Box Internet Radio Recorder (TVBIRR)** is an ansible playbook to configure a X96 Max+ (or similar, if you adjust the playbook) TV box to act as an internet radio streaming and recording device that you can operate entirely with the infrared remote included with the TV box. It includes a playlist of over 32000 free-to-play internet radio stations.

**TVBIRR** uses [music on console (moc)](http://moc.daper.net/) to stream channels and playlists, [keybinder](https://github.com/elopez/keybinder.git) to convert the infrared remote keypresses into commands, [JACK](https://jackaudio.org/) for audio playback and streams are recorded to MP3 files using [jack_capture](https://github.com/kmatheussen/jack_capture). You can see when **jack_capture** is recording the currently playing station because CARD is displayed on the TV box LED display. You can easily customise it to play playlists of locally stored music too and TVBIRR runs in the background leaving your TV box free to serve other tasks.

## Requirements

You need two things to run **TVBIRR**:

An **X96 Max Plus TV box**. At present **TVBIRR** only fully supports the X96 Max+ TV box but you could run it with all of its features, with a little tweaking of its scripts, on any on the TV boxes that have a LED display thats compatible with one of the [armbian-led preset files](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/armbian-docs/led_screen_display_control.md). I'd be willing to accept patches to add support for other TV boxes to TVBIRR.

You also need a Linux compatible USB to audio jack adapter, USB soundcard or USB speaker. Most USB audio devices will work. There are two reasons for this requirement. The onboard audio on the X96 Max pLus only works under Android, not Armbian Linux and its pretty poor quality anyway. Also, A cheap £2 USB headphone jack adapter (I bought 2 for £3) will be much better quality than the onboard audio jack of the X96 Max+.

## Installation

Before you can install or use TVBIRR you must first install [Armbian for Amlogic TV Boxes](https://github.com/ophub/amlogic-s9xxx-armbian) aka s9xxx Armbian. You should be able to install TVBIRR under a Ubuntu or Debian based build of s9xxx Armbian. If you have the X96 Max Plus then you want to download a S905X3 build of Armbian.

OK so you've finally got Armbian running on your TV box! Now you can install TVBIRR by running, as root:

```
apt update
apt upgrade
apt install ansible
git clone https://github.com/danboid/TVBIRR.git
cd TVBIRR
ansible-playbook TVBIRR.yaml
```

## Using TVBIRR

After the ansible playbook has run and installed and configured the various components of TVBIRR, reboot your TV box with a USB audio device attached and you can start using TVBIRR with the remote, without having to have a display attached.

![TVBIRR remote controls](https://github.com/danboid/TVBIRR/blob/main/images/TVBIRR-remote.jpg)

Note that if you are using the default playlist of over 32000 stations, there is a 10 second or so delay before the first station will start to play because moc (the music player) is quite slow at loading large playlists. You can reduce this time by editing the playlist. If you trim the playlist down to about 3K stations moc should load in a second or so when you push play. Once the playlist has loaded, skipping stations is almost instant. If you are going to keep the default 32K station playlist, it might make sense to leave it running and turn the volume down to silent instead of using the stop button because TVBIRR/moc reloads the full playlist each time you push play.

By default, TVBIRR will record its MP3s to the same disk that you are running Armbian on, under /root/mp3s. If you fill your OS disk with MP3s it will stop running so if you do use the record feature it is recommended to adjust the path used by the `jack_capture` recording command in `/etc/keybinder.conf` to save your recordings to a different SD card or an external USB disk.
