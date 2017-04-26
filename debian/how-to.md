## Check out the following ubuntu forums thread for a more in depth overview ## 

https://ubuntuforums.org/showthread.php?t=910717  

## 1) change to the libgphoto-debian repo directory on the linux build machine  ## 

## 2) update all nececessary library (.so) files ## 

## 3) update the package version in DEBIAN/control ## 

## Move up 1 directory level  ## 

## Run `dpkg-deb --build libgphoto-debian` ##

## Copy new .deb file over to firmware repo ## 
