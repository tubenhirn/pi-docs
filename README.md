# plex mediaserver for pi

install a plex mediaserver on a raspberry pi.

## what u\'ll need

-   raspberry pi
-   power supply
-   class 10 sd card
-   installed pi os from <https://www.raspberrypi.com/software/> (pi
    imager)

## install plex mediaserver

1.  update pi os

    ``` bash
    sudo apt-get update
    sudo apt-get upgrade
    ```

2.  install https package

    ``` bash
    sudo apt-get install apt-transport-https
    ```

3.  add pley to keyring

    ``` bash
    curl https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex-archive-keyring.gpg >/dev/null
    ```

4.  add plex repository

    ``` bash
    echo deb [signed-by=/usr/share/keyrings/plex-archive-keyring.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
    ```

5.  update repositories and install plex

    ``` bash
    sudo apt-get update
    sudo apt install plexmediaserver
    ```

## install hfs+ drivers to mount mac hdd\'s

1.  install utils

    ``` bash
    sudo apt-get install hfsplus hfsutils hfsprogs
    ```

2.  get disk uuid for usb drive

    ``` bash
    sudo blkid /dev/<YOUR_DRIVE>
    ```

3.  auto-mount usb drive with fstab config

    ``` bash
    sudo mkdir /mnt/<MOUNT_NAME>
    UUID="<DISK_UUID>" /mnt/<MOUNT_NAME> hfsplus force,auto,uid=1000,users,rw 0 2
    ```

4.  create a folder and set proper permissions

    ``` bash
    sudo chmod 755 -R /mnt/<MOUNT_NAME>/<FOLDER>
    ```

## install and configure netatalk (access drive from a mac device)

1.  install

    ``` bash
    sudo apt-get install netatalk
    ```

2.  configure

    ``` bash
    sudo vim /etc/netatalk/apf.conf
    ```

    -   add the following lines

        ``` bash
        [plex drive]
        path = /mnt/<MOUNT_NAME>
        ```

## configure reboot with crontab

1.  configure reboot once a month (<https://crontab.guru/>)

    ``` bash
    sudo crontab -e

    0 1 1 * * /sbin/shutdown -r now
    ```

## install plex plugin for audiobooks

1.  navigate to the plugins directory

    ``` bash
    cd /var/lib/plexmediaserver/Library/Application Support/Plex Media Server/Plug-ins
    ```

2.  set proper permissions

    ``` bash
    sudo chown -R plex:plex /var/lib/plexmediaserver/Library/Application Support/Plex Media Server/Plug-ins
    ```

3.  clone the plugin with git

    ``` bash
    git clone https://github.com/djdembeck/Audnexus.bundle.git
    ```

## configure default audio device with hifiberry

1.  edit /boot/firmware/config.txt

    -   append

        ``` toml
        [all]
        dtoverlay=hifiberry-dacplus
        ```

    -   remove (existing)

        ``` toml
        dtparam=audio=on
        dtoverlay=vc4-fkms-v3d,audio=off
        dtoverlay=vc4-kms-v3d,noaudio
        ```

2.  find device id for hifiberry

    ``` bash
    aplay -l
    ```

3.  open config-file /etc/asound.conf and configure default device id
    (\<ID\>)

    ``` bash
    pcm.!default { type hw card <ID> } ctl.!default { type hw card <ID> }
    ```

4.  delete user asound.conf (if exists)

    ``` bash
    rm ~/.asoundrc
    ```
