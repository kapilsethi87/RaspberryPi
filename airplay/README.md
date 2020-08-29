# Setup your own Raspberry Pi AirPlay Receiver

### Equipment List

Raspberry Pi 2 or 3

Micro SD Card

Power Supply

Network Connection

Speakers

AirPlay enabled device

### Setting up an Apple AirPlay Receiver

1. Before we get started let’s first run an update and upgrade on our Raspberry Pi to ensure we are running the latest software.
```bash
sudo apt-get update
sudo apt-get upgrade
```

2. Once that has completed we need to install several different packages, run the following commands on your Raspberry Pi to install all of the packages that we need.
```bash
sudo apt-get install autoconf libtool libdaemon-dev libasound2-dev libpopt-dev libconfig-dev
sudo apt-get install avahi-daemon libavahi-client-dev
sudo apt-get install libssl-dev
```

3. We will now clone the shairport-sync source to our Raspberry Pi. Shairport-Sync is the best fork of the original Shairport code and allows syncing across multiple rooms.

Run the following commands on your Raspberry Pi to download the source code to your Raspberry Pi.
```bash
cd ~
git clone https://github.com/mikebrady/shairport-sync.git
```

4. Now that we have cloned the Shairport-Sync repository to our Raspberry Pi we can now build and install the Shairport software.

Before we get started, we must first move into the shairport-sync folder and configure the system. To do this, we must run a few commands on our Raspberry Pi.
```bash
cd shairport-sync
autoreconf -i -f
./configure --with-alsa --with-avahi --with-ssl=openssl --with-systemd --with-metadata
```
The autoreconf command setups the basic config file. The configure command further sets up the build system, telling it to utilize the ALSA audio backend, the Avahi network and set it to use OpenSSL for encryption.

5. With the configuration process now completed we can finally compile Shairport-sync and install it. We can run the two make commands below on our Raspberry Pi to compile and install Shairport-Sync to the device. This process will set up numerous things including the autostart script.
```bash
make
sudo make install
```

6. To enable the Shairport Sync software to start automatically at system startup you need to enter the following command into the terminal on the Raspberry Pi.
```bash
sudo systemctl enable shairport-sync
```

7. Finally, we can start up the Shairport software immediately by running the command below on our Raspberry Pi.
```bash
sudo service shairport-sync start
```

You should now be able to play audio files through your Raspberry Pi AirPlay Receiver using any AirPlay-capable device. If you are using a non-apple device such as an Android device, then there are few apps that allow you to utilize Airplay.

On your AirPlay-enabled device your Raspberry Pi AirPlay receiver should appear as RaspberryPi in the devices list, please note that this name will be the same as your devices hostname. If you would like to know how to change your Raspberry Pi’s hostname, you can check out our raspi-config guide.

---

# Improving the Analogue Audio output


With our Raspberry Pi AirPlay receiver now setup. There are several different things we can do to improve it. The first of these is to change the Raspberry Pi so it will utilize a newer version of the audio driver.

To run this improved audio driver and get the benefits of it fully then there are a few changes we will have to make. If you would like to read about the audio driver, you check out this topic on it on the Raspberry Pi forums.

1. Firstly we need to update the Raspberry Pi’s firmware, and we can do this by running the following command, it can take some time. Make sure your Raspberry Pi doesn’t lose power during this.
```bash
sudo rpi-update
```

2. Once the firmware update has completed, turn off your Raspberry Pi and take out your SD Card. Once you have removed your SD Card, insert the SD Card into a reader connected to a computer. The reason for this is that we need to modify the Raspberry Pi’s boot config file

The file we are after is located at /boot/config.txt on the SD Card, open it up with your favorite text editor. Add the following new line to this file
```bash
audio_pwm_mode=2
```
Once you have edited this file, you can save it and place the SD Card back into your Raspberry Pi and power it back on.

3. With your Raspberry Pi powered back on, there are two more things we need to do before the improved analog audio driver works well with Shairport. The next step is to set it, so the analog jack is the main audio out and not the HDMI output.

We can utilize the following command in the Raspberry Pi’s terminal to do this.
```bash
amixer cset numid=3 1
```

4. Now there is one final thing we must do to finish improving our Raspberry Pi AirPlay device, and that is to modify the volume db Range that Shairport uses. We can modify the range by changing it in the configuration file.

Run the following command to begin editing the configuration file.
```bash
sudo nano /usr/local/etc/shairport-sync.conf
```

5. Within this file make the following changes.
**Find**
```bash
//      volume_range_db = 60 ;
```
**Replace with**
```bash
        volume_range_db = 30;
```
We can now save the file by pressing Ctrl + X then pressing Y and then Enter.

6. Now to make sure all these changes are properly loaded in, we will restart the Raspberry Pi by running the following command.
```bash
sudo reboot
```

---

# Improving your Raspberry Pi AirPlay Receivers Wi-Fi Performance

1. To improve the Wi-Fi performance of your AirPlay device, you will want to disable the Raspberry Pi’s WLAN adaptor power management. This reason for this is that it can prevent Shairport from being visible on your list of Airplay devices due to it powering down the Wi-Fi adaptor.

Luckily it is easy to stop the Raspberry Pi from doing this with most adaptors. The way to do this is by modifying the network interfaces file. We can open the file by using the following command in terminal.
```bash
sudo nano /etc/network/interfaces
```

2. Within this file, we need to locate and add the following text to the interfaces file. This edit will tell the Raspberry Pi not to manage its wireless power and will not turn it off to save power.

If you have upgraded to Raspbian Stretch then wlan0 may be called something different. If you’re unsure which one is the correct one to use, then use the ifconfig command to see what the new name is.
**Find*
```bash
iface wlan0 inet manual
```
**Add Below**
```bash
        wireless-power off
```
We can now save the file by pressing Ctrl + X then pressing Y and then Enter.

3. Now to make sure all these changes are properly loaded in, we will restart the Raspberry Pi by running the following command.
```bash
sudo reboot
```
You should now hopefully have a fully working Raspberry Pi AirPlay receiver.