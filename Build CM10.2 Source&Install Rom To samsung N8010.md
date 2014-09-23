#Build CM10.2 Source&Install Rom To samsung N8010  with Ubuntu14.04 x64 

***

##Step 1: Install official CM10.2 Rom To N8010

###install Recovery
0. install adb & fastboot    
```
sodu apt-get install android-tools-adb
```    
```
sodu apt-get install android-tools-fastboot
``` 
1. DownLoad and install [Heimdall Suite](http://glassechidna.com.au/heimdall/#downloads)   
2. Download [recovery](http://download2.clockworkmod.com/recoveries/recovery-clockwork-6.0.3.6-n8013.img)
3. Power off the Galaxy Note 10.1 (Wi-Fi) and connect the USB adapter to the computer but not to the Galaxy Note 10.1 (Wi-Fi).
4. Boot the Galaxy Note 10.1 (Wi-Fi) into download mode. **Volume Down & Power** Accept the disclaimer on the device. Then, insert the USB cable into the device.
5. At this point, familiarise yourself with the Flashing heimdall notes below so that you are prepared for any strange behaviour if it occurs.
6. On the computer, open a terminal (or Command Prompt on Windows) in the directory where the recovery image is located and type:   
```
sodu heimdall flash --RECOVERY recovery.img --no-reboot
```  
7. A blue transfer bar will appear on the device showing the recovery being transferred.
8. Unplug the USB cable from your device.
9. You can now manually reboot the phone into recovery mode. **Volume Up & Power** Be sure to reboot into recovery immediately after having installed the custom recovery. Otherwise the custom recovery will be overwritten and the device will reboot (appearing as though your custom recovery failed to install).
10. The Galaxy Note 10.1 (Wi-Fi) now has recovery installed. Continue to the next section.   

###Installing CyanogenMod from recovery
1. Download the [CyanogenMod package for your device](http://download.cyanogenmod.org/?device=n8013) for your device that you'd like to install to your computer
2. Boot to recovery mode using **Volume Up & Power**, and connect the phone to your computer through USB.
3. In ClockworkMod Recovery, use the physical volume buttons to move up and down.
4. Optional (Recommended): Select **backup and restore** to create a backup of your current ROM.
5. Select **wipe data/factory reset**.
6. You have two options for transferring and installing the installation packages. Both cases require adb to be working in recovery. The **sideload** method is more universal across devices, whereas the **push and install** method is more commonly used:   
   1) **Sideload method**: select **install zip > install zip from sideload**. Follow the on-screen notices to install the package. The installer does not necessarily display an "Install complete." message. You can tell the install is complete if there were no fatal error messages and you have regained control over the menu.   
   2) **Push and install method**: Open a Terminal and navigate to the directory holding the package(s) you would like to install. On the device, navigate to the **mounts and storage menu**. If you see /storage/sdcard0 or /sdcard as a mountable volume, go ahead and mount it. If you do not see one of these partitions, then instead mount the /data partition. Take note of which volume you mounted. Now, push the package(s) to your device.   
   If you mounted /storage/sdcard0, then: 
     ```
    adb push YourDownloadFileZipFile.zip /storage/sdcard0/
    ```    
    If you mounted /storage/sdcard or /data, then: 
    ```
    adb push YourDownloadFileZipFile.zip /storage/sdcard/
    ``` 
7. Once installation has finished, return to the main menu and select **reboot system now**. The device will now boot into CyanogenMod.


***

##Step 2: Build CyanogenMod Android for Samsung Galaxy Note 10.1 (Wi-Fi) ("n8013")

###Build CyanogenMod and ClockworkMod Recovery
####Prepare the Build Environment
1. **install adb & fastboot**: we already did this before.
2. **Install the Build Packages**: Read [编译Android源码](https://github.com/firegnu/Blog/blob/master/Compile%20Android%20SourceCode%20with%20Ubuntu14.04.md).
3. **Create the directories**: Read [编译Android源码](https://github.com/firegnu/Blog/blob/master/Compile%20Android%20SourceCode%20with%20Ubuntu14.04.md).
4. **Install the repo command**: Read [编译Android源码](https://github.com/firegnu/Blog/blob/master/Compile%20Android%20SourceCode%20with%20Ubuntu14.04.md).
5. **Initialize the CyanogenMod source repository**:    
```
cd ~/cm10.2   
```   
```
repo init -u https://github.com/CyanogenMod/android.git -b cm-10.2
``` 
6. **Download the source code**:    
```
repo sync   
```   
7. **Get prebuilt apps**:    
```
cd ~/cm10.2/vendor/cm  
```    
```
./get-prebuilts  
```  
8. **Prepare the device-specific code**:   
```
source build/envsetup.sh 
```   
```
breakfast n8013
```   
maybe when you run **breakfast n8013** command, you will get something wrong for download the device specific configuration and kernel source for your device. you should edit 
**~/cm10.2/.repo/manifest.xml** file(you can add [local_manifest for n80xx](https://github.com/firegnu/Blog/blob/master/samsung%20n80XX%20local_manifest.xml) content to your **~/cm10.2/.repo/manifest.xml**) and run again.
9. **Extract proprietary blobs**: Now ensure that your Galaxy Note 10.1 (Wi-Fi) is connected to your computer via the USB cable and that you are in the ~/android/system/device/samsung/n8013 directory (you can cd ~/cm12/device/samsung/n8013 if necessary). Then run the **extract-files.sh** script:   
```
./extract-files.sh
```    
You should see the proprietary files (aka “blobs”) get pulled from the device and moved to the ~/cm10.2/vendor/samsung directory.
10. **Turn on caching to speed up build**: Read [编译Android源码](https://github.com/firegnu/Blog/blob/master/Compile%20Android%20SourceCode%20with%20Ubuntu14.04.md).
11. Start the build:   
 ```
 croot
 ```    
 ```
 brunch n8013
 ```    
 
###Install the build
Assuming the build completed without error (it will be obvious when it finishes), type:   
 ```
 cd $OUT
 ```    
 in the same terminal window that you did the build. Here you’ll find all the files that were created. The stuff that will go in /system is in a folder called system. The stuff that will become your ramdisk is in a folder called root. And your kernel is called... kernel.   
 But that’s all just background info. The two files we are interested in are (1) recovery.img, which contains ClockworkMod recovery, and (2) cm-[something].zip, which contains CyanogenMod.   
 Then follow Step1 to install cm10.2 to N8010.

 