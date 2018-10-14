## install NOOBS on SD card
Follow instructions from http://qdosmsq.dunbar-it.co.uk/blog/2013/06/noobs-for-raspberry-pi/

Grosso modo this corresponds to:
1) On the SD card, create one unique primary partition with type W95 FAT32 (it corresponds to `fdisk` type code "b")
2) Although we have told `fdisk` to format the partition, we have yet to format it with `sudo mkfs.vfat /dev/mmcb1k0p1`
3) Mount the SD card with
```
sudo mkdir /tmp/root 
sudo mount /dev/mmcb1k0p1 /tmp/root
```
4) Unzip the NOOBS data into the SD card
```
cd /tmp/root
unzip ~/Downloads/NOOBS_v2_0_9.zip
```
5) unmount the SD card
6) Put it into the raspberry, boot and follow NOOBS instructions

## Set wifi
WEP protected wifi was not configurable with the network GUI. It led to an error like "cannot connect, pre shared key might be too long" after entering the wep key.
A solution has been to edit the file `/etc/wpa_supplicant/wpa_supplicant.conf` by hand to get
```
interface=/var/run/wpa_supplicant
ctrl_interface_group=pi
network={
	ssid="XXXXXXXX"
	scan_ssid=1
	key_mgmt=NONE
	wep_key0=XXXXXXXXXXX
	wep_tx_keyidx=0
}
```
with "ssid" the name of the wifi, visible through the network GUI, and "wep_key0" the wep key.

Then
```
sudo killall wpa_supplicant
sudo wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
```

## Post-installation
```
sudo apt update
sudo apt upgrade
```
Then install packages
```
firefox-esr vim git
```
By default chromium is the unique browser (it has been customized for raspberry low RAM). In the packages available only firefox-esr flavour is available.

Enable ssh server
```
sudo systemctl start ssh
sudo systemctl enable ssh
```

## attempt to install Arch linux ARM
* ARMv8 (https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3) didn't work, cannot even start to boot.

* ARMv7 (https://archlinuxarm.org/platforms/armv7/broadcom/raspberry-pi-2) didn't not work out-of-the-box for the B+ model neither.
See https://archlinuxarm.org/forum/viewtopic.php?f=67&t=12805 and https://archlinuxarm.org/forum/viewtopic.php?f=65&t=12661 for more info.
It seems that the kernel.img isn't loaded durting boot (4, or maybe 3, green ligh flashes during boot) and no signal is detected by the HDMI screen at all.
