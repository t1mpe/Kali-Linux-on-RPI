
# Kali Linux on Raspberry Pi

I decided to create this tutorial since I needed a place to store some of the commands needed when installing Kali/SSH/VNC/Alfa adapter on Raspberry Pi 4.

### Contents:
1. Flashing Kali linux on MicroSD card
2. Booting Kali Linux
3. SSH Connection
4. Updating / New credentials
5. Setting up VNC
6. Installing Alfa Network adapter (AWUS036ACH)
---

1 - Flashing Kali Linux on MicroSD card 
-

 1. Download Kali Linux image (ARM / Raspberry Pi) from: [`https://www.kali.org/get-kali/#kali-arm`](https://www.kali.org/get-kali/#kali-arm)
 2. Download and install Balena Etcher which is used to flash Kali image on to MicroSD card. You can get it here: [`https://www.balena.io/etcher/`](https://www.balena.io/etcher/)
 3. Run Balena Etcher and select `"Flash from file"`
 4. Find the Kali Linux image file downloaded in step 1 and select it as the file you want to flash
 5. Insert MicroSD car into your computer/reader
 6. In Balena Etcher proceed to `"Select target"` and check the sd-card you want to use
 7. Once source and target are chosen select `"Flash!"`
 8. Flashing could take couple of minutes depending on the speed of the MicroSD card

Once the flashing process is complete, you can remove the MicroSD card and insert it into your Raspberry Pi.

2 - Booting Kali Linux
-
To confirm that the flashing and installation of Kali Linux was successful, I like to connect display and keyboard to the Raspberry Pi before booting up.

 1. Boot Raspberry Pi
 2. Wait for the installation to complete
 3. Once you see the login screen you can use these default credentials to login: `username: kali`  `password: kali`

You can continue using Kali linux like this with dedicated display and peripherals, or continue this tutorial to see other ways to use it without them.

3 - SSH Connection
-
 1. Connect Raspberry Pi to your router using ethernet cable
 2. Find the local IP-address of your RPI in your routers dashboard ("network map" or something like that)
 3. Open terminal on your computer and type: `ssh kali@"YOUR_RPI_IPADDRESS`
 4. Provide the default password `kali`
 
 Now you should have successfully established a SSH connection to Kali Linux running on your RPI.

4 - Updating / New credentials
-
#### Updating
One of the first things to do with a fresh linux install is to update it.

 1. Either by using GUI terminal or SSH connection run the following command: `sudo apt update`
 2. Next run `sudo apt full-upgrade -y`
 3. After installation is complete, reboot Kali using `sudo reboot now`

Your Kali linux should now be up to date.

#### Credentials

Changing the default credentials is next on our list. Kali linux uses default username/password combo `kali / kali` for basic user access and `root / toor` for root level access.

To change passwords:

 1. `sudo passwd root`
 2. provide default root password `toor`
 3. enter new password two times
 4. And you're done.

You can use this same procedure to change password for user `kali` (`sudo passwd kali`...).

5 - Setting up VNC
-
With VNC (virtual network computing) protocol you can use your Kali linux GUI over the network remotely.

I found it best to run installation as root (sudo did not work for me)

 1. Change user to **root** using su (switch user) command in terminal: `su root`
 2. Run: `apt-get update && apt-get isntall -y x11vnc`
 3. After installation is complete reboot Kali using: `reboot now` 
 4. Next we edit /boot/config.txt file, run: `sudo nano /boot/config.txt`
 5. Locate the following rows and change the parameters as follows:
	```console
	disable_overscan=1
	framebuffer_width=1920
	framebuffer_height=1080
	```

	Framebuffer defines the resolution for your VNC screen.
	**Remember to remove # from the beginning of the row!**

 6. Press `Ctrl+X` to save the changes you've made
 7. Reboot system again `sudo reboot now`
 8. Create a password for your VNC connection using: `sudo x11vnc -storepasswd` and write it to file when prompted.
 9. To configure x11vnc run: `sudo x11vnc -ncache -auth guess -nap -forever -loop -repeat -rfbauth /root/.vnc/passwd -rfbport 5900`
 10. To have x11vnc start automatically when system starts up we need to first create new service for it. Create new file using: `sudo nano /etc/systemd/system/vnc.service`
 11. Add this into the file: 
		```console
		[Unit]
		Description = X11VNC pre-login
		After = multi-user.target
		[Service]
		Type = simple
		ExecStart = /usr/bin/x11vnc -nonc -display :0 -auth guess -nap -forever -loop -repeat -rfbauth /root/.vnc/passwd -rfbport 5900
		[Install]
		WantedBy = multi-user.target
		```
 12. Activate the service using: 
 13. `sudo systemctl daemon-reload`
 14. To make this service automatically start, run: `sudo systemctl enable vnc`
 15. And lastly we will reboot once more `sudo reboot now`

I prefer using VNC Viewer -client on macOS for connecting to Kali linux. To connect using VNC with the client of choice you provide it with the IP-address of RPI, the port number 5900 we defined earlier and the password we set for the VNC.

6 - Installing Alfa Network adapter (AWUS036ACH)
-
I have Alfa 1200 ultra range AWUS036ACH network adapter that  I use e.g. for wardrinwing etc. To install drivers for this specific adapter follow these instructions:

 1. To install dkms package run: `sudo apt-get install realtek-rtl88xxau-dkms`
 2. Change directory to Desktop using: `cd Desktop`
 3. To download drivers run: `sudo git clone https://github.com/aircrack-ng/rtl8812au.git`
 4. Change directory to `rtl8812au` (the one we just downloaded) using: `cd rtl8812au`
 5. Once inside the correct directory tun: `sudo make` and after it has completed run `sudo make install`
 6. And as we've done before, reboot the system using: `sudo reboot now`

The drivers should now be installed and you can start using the adapter.

