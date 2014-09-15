#Compile Android SourceCode with Ubuntu14.04

##环境准备
    1.基本环境:ubuntu-14.04-desktop-64bit LTS(32位版本无法编译，裸机或者虚拟机均可).
    2.空闲磁盘空间: 100G以上,其中源码接近10G,编译过程中的中间文件和最终目标接近100G.


***

##环境初始化
* 安装Oracle JDK(Android4.4以上版本不支持OpenJDK,必须使用Oracle JDK1.6 X64版本)
    - 判断操作系统是32位还是64位
        - 利用 
        ```
        uname -a
        ```
        来判断系统是32位或者64位
    - 卸载OpenJDK
        - 查看是否安装OpenJDK,如果已经安装,会显示java信息,如果安装了OpenJDK,用如下命令卸载
        ```
        sudo apt-get purge openjdk-\*
        ```
    - 下载Oracle JDK
        需要下载Oracle 1.6 X64位版本   
        打开[link](http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-javase6-419409.html)选择
          ```
          Java SE Development Kit 6u45
          ``` 
          ,下载即可
    - 安装Oracle JDK   
        创建java目录   
          1.```
          sudo mkdir -p /usr/local/java
          ```   
          2.```
          cd /usr/local/java
          ```   
          3.```
          sudo cp ~/Downloads/jdk-6u45-linux-x64.bin .
          ```   
          4.```
          sudo chmod +x jdk-6u45-linux-x64.bin
          ```   
          5.```
          sudo ./jdk-6u45-linux-x64.bin
          ```   
          6.```
          sudo rm -rf jdk-6u45-linux-x64.bin
          ```   
    - 配置Oracle JDK   
          1.配置JAVA_HOME和PATH环境变量，在/etc/profile文件末尾加入以下部分   
            ```
            JAVA_HOME=/usr/local/java/jdk1.6.0_45    
            ```    
            ```
            PATH=$PATH:$HOME/bin:$JAVA_HOME/bin    
            ```   
            ```
            export JAVA_HOME  
            ```   
            ```
            export PATH  
            ```   
          2.配置ubuntu的JDK和JRE的位置   
            ```
            sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/jdk1.6.0_45/bin/java" 1  
            ```     
            ```
            sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/java/jdk1.6.0_45/bin/javac" 1 
            ```   
            ```
            sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/local/java/jdk1.6.0_45/bin/javaws" 1
            ```   
          3.配置Oracle JDK为系统默认JDK/JRE     
             ```
             sudo update-alternatives --set java /usr/local/java/jdk1.6.0_45/bin/java 
             ```     
             ```
             sudo update-alternatives --set javac /usr/local/java/jdk1.6.0_45/bin/javac
             ```   
             ```
             sudo update-alternatives --set javaws /usr/local/java/jdk1.6.0_45/bin/javaws
             ```   
    - 配置环境使其生效   
        ```
        . /etc/profile
        ```   
         再次执行```
         java -version
         ```应该显示如下:   
         ```
         java version "1.6.0_45"
         ```     
         ```
         Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
         ```     
         ```
         Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)
         ```  
* 安装必备软件   
         1.   
         ```
         sudo apt-get install git gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-dri:i386 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386 dpkg-dev
         ```   
         2.   
         ```
         sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so
         ```  
* 配置USB访问权限   
         将如下内容加入到/etc/udev/rules.d/51-android.rules文件中   
         ```
         # adb protocol on passion (Nexus One)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e12", MODE="0600", OWNER="<username>"   
         # fastboot protocol on passion (Nexus One)
         SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", ATTR{idProduct}=="0fff", MODE="0600", OWNER="<username>"
         # adb protocol on crespo/crespo4g (Nexus S)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e22", MODE="0600", OWNER="<username>"
         # fastboot protocol on crespo/crespo4g (Nexus S)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e20", MODE="0600", OWNER="<username>"
         # adb protocol on stingray/wingray (Xoom)
         SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", ATTR{idProduct}=="70a9", MODE="0600", OWNER="<username>"
         # fastboot protocol on stingray/wingray (Xoom)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="708c", MODE="0600", OWNER="<username>"
         # adb protocol on maguro/toro (Galaxy Nexus)
         SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", ATTR{idProduct}=="6860", MODE="0600", OWNER="<username>"
         # fastboot protocol on maguro/toro (Galaxy Nexus)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e30", MODE="0600", OWNER="<username>"
         # adb protocol on panda (PandaBoard)
         SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d101", MODE="0600", OWNER="<username>"
         # adb protocol on panda (PandaBoard ES)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="d002", MODE="0600", OWNER="<username>"
         # fastboot protocol on panda (PandaBoard)
         SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d022", MODE="0600", OWNER="<username>"
         # usbboot protocol on panda (PandaBoard)
         SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d00f", MODE="0600", OWNER="<username>"
         # usbboot protocol on panda (PandaBoard ES)
         SUBSYSTEM=="usb", ATTR{idVendor}=="0451", ATTR{idProduct}=="d010", MODE="0600", OWNER="<username>"
         # adb protocol on grouper/tilapia (Nexus 7)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e42", MODE="0600", OWNER="<username>"
         # fastboot protocol on grouper/tilapia (Nexus 7)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4e40", MODE="0600", OWNER="<username>"
         # adb protocol on manta (Nexus 10)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee2", MODE="0600", OWNER="<username>"
         # fastboot protocol on manta (Nexus 10)
         SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", ATTR{idProduct}=="4ee0", MODE="0600", OWNER="<username>"
         ```  
* 配置ccache   
         安装ccache并将export USE_CCACHE=1加入到~/.bashrc中   
         ```
         sudo apt-get install ccache
         ```     
         ```
         source ~/.bashrc
         ```  
         
***


##源代码下载
* 安装repo      
   ```
   mkdir ~/bin
   ```     
   ```
   PATH=~/bin:$PATH
   ```   
   ```
   curl http://git-repo.googlecode.com/files/repo-1.12 > ~/bin/repo
   ```     
   ```
   chmod a+x ~/bin/repo 
   ```   
* 初始化repo   
```
repo init -u https://android.googlesource.com/platform/manifest -b android-4.4.2_r2
```     
注意：如果是在虚拟机中执行,需要采用email验证的方式,详见[link](http://source.android.com/source/downloading.html)   
```
git config --global user.email "you@example.com"
```   
```
git config --global user.name "Your Name"
```   
修改aosp/.resp/manifest.xml,将fetch=".."改为:   
```
fetch="https://android.googlesource.com/"
```  
         ```  
         
***


##源代码下载
* 源码同步   
```
$repo sync
```    
此过程漫长又折磨(由于你懂的原因,需要全程挂载vpn)
         
***


##源代码编译
* 设置ccache  
```
cd aosp
```    
```
prebuilts/misc/linux-x86/ccache/ccache -M 50G  
```    
* 初始化编译环境   
```
. build/envsetup.sh
```    
* 选择编译目标包(目标包的选择跟你的硬件有关,虚拟机跑的话可以选1) 并开始编译(此过程视硬件而定,大约5个小时),编译成功后会在out目录下生成img文件.    
```
lunch
```   
```
make -j4
```   
j4中的数字跟你的CPU个数和核数有关,视情况而定.
* 编译完成后启动虚拟机   
```
emulator &
```   
如果提示找不到emulator命令，可将如下环境变量重新初始化一遍,lunch后面的参数要与你编译的ROM选项一致   
```
. build/envsetup.sh
```   
```
lunch aosp_arm-eng 
```   
         
***

##将img加载到物理机
* 将手机用USB数据线连接到机器
* 安装fastboot工具   
```
sudo apt-get install android-tools-fastboot
```   
* 设置ANDROID_PRODUCT_OUT环境变量   
将如下部分加入~/.bashrc文件中:   
```
export ANDROID_PRODUCT_OUT=/home/dennis/devenv/aosp/out/target/product/***
```   
***视编译的目标为准(例如mako等等),然后保存执行". .bashrc",使其生效.   
* 以fastboot模式重启手机    
```
adb reboot bootloader 
```     
此时手机会重启，呈现一个与往不同的fastboot界面