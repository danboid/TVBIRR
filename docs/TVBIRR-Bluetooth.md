# Using TVBIRR as a Bluetooth speaker

With a little bit of configuration, you can pair your phone or tablet to your TVBIRR install and have it play a Bluetooth audio stream sent wirelessly from your phone, tablet or other Bluetooth device. Thanks to TVBIRR being based upon JACK, you can stream Bluetooth audio at the same time as playing internet streams with `moc`, if you wish.

It should be possible to pair and connect multiple Bluetooth devices and have them streaming simulateously to `bluez-alsa` but I've not tested this type of configuration.

Most of these steps are only required to be done once but some of these need to be repeated for every new Bluetooth device you wish to stream from.

## Why is Bluetooth setup not automated by the TVBIRR playbook?

There are a few reasons for this guide and the playbook not configuring Bluetooth, except for configuring the Stop Bluetooth key:

* There are several different versions of the X96 Air and X96 Max+ and not all of them feature integrated Bluetooth. The user may have a non-BT model and may not own a USB BT adapter.

* There are a few steps in the pairing process that cannot be automated with an ansible playbook.

* The user may not want to use Bluetooth at all.

## Does my TV box have working onboard Bluetooth?

Having working Bluetooth under Android on your TV box doesn't mean it will also work under Armbian unless it is correctly configured in the your dtb (Device Tree Blob) file. You can check the [Amlogic Armbian model database](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/build-armbian/armbian-docs/amlogic_model_database.md) to see if Bluetooth is listed as being supported for your model.

The X96 Air Q1000 and X96 Max Plus Q2 are both known to have a Armbian .dtb file that has working onboard Bluetooth support - `meson-sm1-x96-max-plus-q2.dtb`. If you can't find a dtb with working Bluetooth for your TV box then you can always use a USB Bluetooth adapter instead. Most if not all USB Bluetooth adapters will work with Linux.

## Install required packages

Ubuntu 22.04 and later users can install the bluez Bluetooth configuration tools and bluez-alsa-utils by running:

```shell
$ sudo apt install bluez bluez-alsa-utils
```

## Check your Bluetooth adapter is working

Before we go any further, lets check Armbian recognises your Bluetooth adapter:

```shell
$ bluetoothctl show
```

This command will show the name of your Bluetooth controller if it has been recognised by the Linux kernel.

## Configure the bluez-alsa.service systemd service

Edit `/etc/default/bluez-alsa` and modify the `OPTIONS` line so that it reads:

```
OPTIONS="-p a2dp-sink"
```

This enables the bluez-alsa Bluetooth audio receiver profile.

## Create the bluealsa-aplay systemd service

Create the file `/lib/systemd/system/bluealsa-aplay.service` containing:

```
[Unit]
Description=Bluealsa audio player
Documentation=https://github.com/Arkq/bluez-alsa/
Wants=bluealsa.service

[Service]
Type=simple
ExecStart=/usr/bin/bluealsa-aplay
Restart=on-failure
ProtectSystem=strict
ProtectHome=true
PrivateTmp=true
RemoveIPC=true
RestrictAddressFamilies=AF_UNIX

[Install]
WantedBy=bluetooth.target
```

Make this service start on boot by running:

```shell
$ sudo systemctl enable bluealsa-aplay.service
```

You may now want to reboot to ensure the bluez-alsa services are running before proceeeding.

## Connect your Bluetooth device

Enter the `bluetoothctl` console by running `bluetoothctl` with no extra options then run `scan on`. Enable the Bluetooth connection on your device and you should see it recognised with its MAC address listed in the `bluetoothctl` console within a few seconds.

Pair your bluetooth device by running `pair XX:XX:XX:XX:XX:XX` followed by `trust XX:XX:XX:XX:XX:XX` then `connect XX:XX:XX:XX:XX:XX`, replacing the X's with your devices Bluetooth MAC address. After you have paired your device you can quit the bluetoothctl console by typing `exit` or `quit`. You usually only need to perform these steps the first time you connect a device.

Your TV box should now be visible on the Bluetooth settings menu on your device as a connected Bluetooth speaker called `armbian`. You should now be ready to start streaming audio to it provided Bluetooth is enabled on the connecting machine and the device is within range.
