# OttoTouch

## Hardware

- Raspberry Pi 4 2GB/4GB/8GB
- Raspberry Pi 4 official power supply
- Raspberry Pi 7" Touchscreen display
- MicroSD card (min. 16GB)

## Hardware preparation

1. See [here](https://community.element14.com/products/raspberry-pi/w/documents/888/raspberry-pi-7-touchscreen-display) to install the touchscreen.

## OS installation

1. Flash the Rasberry Pi OS lite 64-bit image to the MicroSD card.
1. Mount the /boot partition and create two files:
   - `touch ssh` to enable the SSH server
   - `userconf.txt` with the content `username:hash`. Generate the password hash using `openssl passwd -6`
1. Insert the MicroSD card into the Raspberry Pi and boot up.
1. Login, execute `raspi-config`.
   - Set up wi-fi connection
   - Expand the file system
   - Reboot
1. Install dependencies: `apt install python3-dev build-essential`.
2. Install OctoPrint: `pipx install octoprint`.
3. Create systemd service file `sudoedit /etc/systemd/system/octoprint.service` with the following content (replace user pi as appropriate):
    ```
    [Unit]
    Description=OctoPrint
    After=network.target

    [Service]
    User=pi
    ExecStart=/home/pi/.local/bin/octoprint serve
    Restart=always
    Nice=-2

    [Install]
    WantedBy=multi-user.target
    ```
4. Start the OctoPrint service:
   ```
   sudo systemctl daemon-reexec
   sudo systemctl daemon-reload
   sudo systemctl enable octoprint
   sudo systemctl start octoprint
   ```
5. Check status: `sudo systemctl status octoprint`.
6. To allow OctoPrint to reboot and shutdown the hardware, create a sudoer file `sudoedit /etc/sudoers.d/octoprint-shutdown` with the following content:
   ```
   pi ALL=NOPASSWD: /usr/bin/systemctl restart octoprint
   pi ALL=NOPASSWD: /usr/bin/systemctl reboot
   pi ALL=NOPASSWD: /usr/bin/systemctl poweroff
   ```
