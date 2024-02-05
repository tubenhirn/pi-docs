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

## install vim

``` bash
sudo apt-get install vim
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
