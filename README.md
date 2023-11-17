# ME7Logger with CH34X
Logging ME7.5 ECU Live data with ME7Logger and CH34* cable

## Preparation

1. Download setzi62's ME7Logger from  
    http://nefariousmotorsports.com/forum/index.php/topic,837.0title,.html  
    1.1 *Linux Build*: **ME7Logger_Linux_DIST_10072013_v1.20.tgz**  
    1.2 *Windows Build*: **ME7Logger_DIST_10072013_v1.20.zip**
2. Install the driver for your CH34X based cable from  
    2.1 *Windows*: https://www.wch-ic.com/downloads/CH341SER_EXE.html  
        2.1.1 Just run the installer with your cable disconnected  
        2.1.2 After installation plug-in your cable and check `Device Manager`  
    2.2 *Linux*: https://www.wch-ic.com/downloads/CH341SER_LINUX_ZIP.html  
        2.2.1 `unzip CH341SER_LINUX.ZIP && cd CH341SER_LINUX/`  
        2.2.2 ```sh  
              # Arch
              make -si
              # Debian
              make && sudo make install
              ```  
