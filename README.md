# TVBIRR

**TV Box Internet Radio Recorder (TVBIRR)** is an Ansible playbook to configure a X96 Air or X96 Max(+) TV box to function as an internet radio streaming and recording device that you can operate entirely with the infrared remote included with the TV box. It includes a playlist of over 21000 free-to-play internet radio stations and text-to-speech support to tell you over the speakers what you're listening to.

![X96 Max Plus pic](https://github.com/danboid/TVBIRR/blob/main/images/x96Max%2B.jpg)

**TVBIRR** uses [music on console (moc)](http://moc.daper.net/) to stream channels and playlists, [keybinder](https://github.com/elopez/keybinder.git) to convert the infrared remote keypresses into commands, [JACK](https://jackaudio.org/) for audio playback, streams are recorded to MP3 files using [jack_capture](https://github.com/kmatheussen/jack_capture) and [espeak](http://espeak.sourceforge.net/) is used to speak the names of titles (tracks or shows) and stations.

You can see when **jack_capture** is recording the currently playing station because **CARD** is displayed on the TV box LED display. You can easily customise it to play playlists of locally stored music. TVBIRR runs in the background leaving your TV box free to serve other tasks.

## Requirements

You need two things to run **TVBIRR**:

A **X96 Air**, **X96 Max Plus** or **X96 Max** TV box. At present **TVBIRR** only fully supports those TV boxes because they all use the same openvfd LED display configuration. You should still be able to run **TVBIRR**  with all of its features, with a little tweaking of its scripts, on any on the TV boxes that have a LED display thats compatible with one of the [armbian-led preset files](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/armbian-docs/led_screen_display_control.md). I'd be willing to accept patches to add support for other TV boxes to **TVBIRR**.

You also need a Linux-compatible USB to audio jack adapter, USB soundcard or USB speaker. Most USB audio devices will work. There are two reasons for this requirement. The onboard audio on the X96 Max Plus only works under Android, not Armbian Linux and its pretty poor quality anyway. Also, A cheap £2 USB headphone jack adapter (I bought 2 for £3) will provide much better audio quality than the onboard audio jack of most budget TV boxes.

## Installation

Before you can install or use TVBIRR you must first install [Armbian for Amlogic TV Boxes](https://github.com/ophub/amlogic-s9xxx-armbian) aka s9xxx Armbian. You should be able to install TVBIRR under a Ubuntu or Debian-based build of s9xxx Armbian. If you have a X96 Air or an X96 Max Plus then you want to download a S905X3 build of Armbian, S905X3 being the model of the System On a Chip (SoC) used in the X96 Air and the X96 Max+.

TIP: `nmtui` is useful for easily configuring your wifi from the Armbian console, if you are using wifi.

When you've got Armbian running on your TV box, you can install TVBIRR by running the following commands as root:

```
apt update && apt upgrade
apt install ansible
git clone https://github.com/danboid/TVBIRR.git
cd TVBIRR
ansible-playbook TVBIRR.yaml
```

## Using TVBIRR

After the Ansible playbook has been run and its installed and configured the various components of TVBIRR, reboot your TV box with a USB audio device attached and you can start using TVBIRR with the remote, without having to have a HDMI display attached to your TV box. You can push the play button to start playing the default playlist as soon as the LED clock lights up.

![TVBIRR remote controls](https://github.com/danboid/TVBIRR/blob/main/images/TVBIRR-remote.jpg)

Note that if you are using the default playlist of over 21000 stations, there is a 10-15 second+ delay before the first station will start to play because moc, the music player, is quite slow at loading large playlists so be patient! The maintainer of moc has been made aware of this issue and is working on a fix. You may be able to reduce this initial startup time by reducing the size of the default playlist, `/root/playlists/TVBIRR-All-Stations.m3u`. Once the playlist has loaded, skipping stations is almost instantaneous.

If you are using the default station playlist, it might make sense to leave it running and turn the volume down to silent instead of using the stop button because TVBIRR/moc reloads the full playlist each time you push the play button on the remote.

By default, TVBIRR will record its MP3s to the same disk that you are running Armbian on, under `/root/mp3s`. Be warned that if you fill your OS disk with MP3s your TV box will stop running and likely won't boot so if you do use the record feature it is recommended to adjust the path used by the `jack_capture` recording command in `/etc/keybinder.conf` to save your recordings to a different MicroSD card or an external USB disk.

## Using TVBIRR as a Bluetooth speaker

You can configure your TVBIRR setup to simultaneously function as a wireless speaker/amplifier for Bluetooth devices like phones and tablets with some [extra configuration.](https://github.com/danboid/TVBIRR/blob/main/docs/TVBIRR-Bluetooth.md)

## Customising /etc/keybinder.conf

If you want to change the action of the IR remote buttons you need to find out what values to use with keybinder for each button. Run the following command then push buttons on your remote to print their codes. You can assign commands to these keycodes in `/etc/keybinder.conf`:

```
evtest /dev/input/by-path/platform-ff808000.ir-event
```

Note that the default IR button actions are hardcoded into the kernel so you have to edit the relevant bit of the meson-ir kernel source and rebuild the kernel to disable the power button, for example.

## Generating the default playlist

TVBIRR includes a default playlist of over 21000 stations but if you wanted to generate the playlist yourself, run:

```
curl -L http://de1.api.radio-browser.info/json/stations > radiobrowser.json
jq '.[].url_resolved' radiobrowser.json > TVBIRR-All-Stations.m3u
sed -i '/^"https/d;s/"//g;/^$/d' TVBIRR-All-Stations.m3u
```
