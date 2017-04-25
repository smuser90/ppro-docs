cd ~/pulse-pro-yocto/build-fb01  

bitbake fsl-image-machine-test  

cd ./tmp/deploy/images/imx6ulevk  

## Plug the sd card into the machine ##

dmesg | tail | grep sd  

## locate the device it was enumarated as (usually sdf, sometimes sdd) ##

## Now make sure it is not mounted on the file system ##

sudo umount /media/alpine/*  

## Copy the sdcard image to the device ##

sudo dd if=fsl-image-machine-test-imx6ulevk.sdcard of=/dev/sdf bs=1M && sync  

## Remove and replace the sd card to ensure it gets mounted in /media/alpine ##

cd ~/pulse-pro-yocto/linux-imx  

. /opt/fsl-imx-fb/3.14.52-1.1.1/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi  

make zImage dtbs modules -j8  

sudo cp arch/arm/boot/dts/imx6ul-14x14-evk-btwifi.OOB_IRQ.dt* /media/alpine/Boot\ imx6ul/  

sudo cp arch/arm/boot/zImage /media/alpine/Boot\ imx6ul/  


## That's it! The sdcard is now prepared for booting. Now unmount it ##
sudo umount /media/alpine/*  

## Boot the imx6ul evk and interrupt u-boot by pressing any key ##
## Change the device tree blob to be for OOB Interrupts ##

setenv fdt_file imx6ul-14x14-evk-btwifi.OOB_IRQ.dtb  

saveenv  

boot  

## After Boot run the following command ##
update-rc.d -f hostapd remove  

## Add the following line to /etc/rc.local after 'echo 30000 > /proc/sys/vm/min_free_kbytes' ##
exec /bin/login -f root  

## Create /home/root/.profile and add the following line to it ##
cd /home/root/pulse-nodejs; nice --20 node main.js&  

## You now have a system that will auto-login and run the firmware ##

## As a final step, git pull the firmware ##
cd /home/root; git pull https://github.com/smuser90/pulse-nodejs.git  

## reboot and everything should 'just work' ##
