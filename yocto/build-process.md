cd ~/pulse-pro-yocto/build-fb01  

bitbake fsl-image-machine-test  

cd ./tmp/deploy/images/imx6ulevk  

## Plug the sd card into the machine ##

dmesg | tail | grep sd  

## locate the device it was enumarated as (usually sdf, sometimes sdd) ##

## Now make sure it is not mounted on the file system ##

sudo umount /media/alpine/*  
## may get umount: /media/alpine/*: mountpoint not found which is fine ##

## Copy the sdcard image to the device ##

sudo dd if=fsl-image-machine-test-imx6ulevk.sdcard of=/dev/sdf bs=1M && sync  

## Remove and replace the sd card to ensure it gets mounted in /media/alpine ##
This next step loads in the murata image files

cd ~/pulse-pro-yocto/linux-imx  

. /opt/fsl-imx-fb/3.14.52-1.1.1/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi  

make zImage dtbs modules -j8  

sudo cp arch/arm/boot/dts/imx6ul-14x14-evk-btwifi.OOB_IRQ.dt* /media/alpine/Boot\ imx6ul/  

sudo cp arch/arm/boot/zImage /media/alpine/Boot\ imx6ul/  


## That's it! The sdcard is now prepared for booting. Now unmount it ##
sudo umount /media/alpine/*  

## To terminal into the imx board, plug the USB cable into your laptop, run " screen /dev/tty.S  115200 " and hit tab to fill the rest of the name in ## 
(Note that you will need a special driver- http://www.silabs.com/documents/public/software/Mac_OSX_VCP_Driver.zip)

## Boot the imx6ul evk and interrupt u-boot by pressing any key ##
## Change the device tree blob to be for OOB Interrupts ##

setenv fdt_file imx6ul-14x14-evk-btwifi.OOB_IRQ.dtb  

saveenv  

boot  
## (it will result in failutres for a little while, need to let them finish) ## 
## After Boot, login with "root" and run the following command 
update-rc.d -f hostapd remove  

## Add the following line to /etc/rc.local after 'echo 30000 > /proc/sys/vm/min_free_kbytes' ##
exec /bin/login -f root  
(to edit the rclocal file i have to call vi /etc/rc.local hit “a” to be able to edit. after editing hit “esc, : , w+q”)

## Connect ethernet cable to port and grab an IP address with the following command ## 
 
 dhclient eth0

## Pull firmware from github ## 
 
 git pull https://github.com/smuser90/pulse-nodejs.git
 
## Create /home/root/.profile and add the following line to it ##
cd /home/root/pulse-nodejs; nice --20 node main.js&  

## You now have a system that will auto-login and run the firmware ##

## As a final step, git pull the firmware ##
cd /home/root; git pull https://github.com/smuser90/pulse-nodejs.git  

## reboot and everything should 'just work' ##
