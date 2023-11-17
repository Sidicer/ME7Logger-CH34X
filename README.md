# ME7Logger with CH34X
Logging ME7.5 ECU Live data with ME7Logger and (cheap/clone) CH34* cable  
*You need to have your ECU's map file (.bin) already either by Reading your ECU with NefMoto or MPPS or by downloading stock map from the internet*

## tl;dr

```sh
connection: 0x00
baud: 125000
samples: 50
# No need to remove cluster fuse or pull any wires from ecu to obd port :)
```

## Pre-requisites

1. Download setzi62's ME7Logger from  
    http://nefariousmotorsports.com/forum/index.php/topic,837.0title,.html  
    1.1 *Linux Build*: **ME7Logger_Linux_DIST_10072013_v1.20.tgz**  
    1.2 *Windows Build*: **ME7Logger_DIST_10072013_v1.20.zip**
2. Install the driver for your CH34X based cable from  
    2.1. *Windows*: https://www.wch-ic.com/downloads/CH341SER_EXE.html  
        2.1.1. Just run the installer with your cable disconnected  
        2.1.2. After installation plug-in your cable and check `Device Manager`  
    2.2 *Linux*: https://www.wch-ic.com/downloads/CH341SER_LINUX_ZIP.html  
        2.2.1. `unzip CH341SER_LINUX.ZIP && cd CH341SER_LINUX/`  
        2.2.2. `make -si # Archlinux`  
        2.2.2. `make && sudo make install # Debian`

## Preparation

0. `cd /path/to/ME7Logger`  
1. Place your `.bin` file in `ME7Logger/images` 
2. Generate an ECU definition from your maps binary  
    ```sh
    ./bin/ME7Info images/your-binary.bin
    # This will create an your-binary.ecu definition file in ME7Logger/ecus
    ```
3. Generate a configuration file (what to log) from the same binary  
   ```sh
   ./bin/ME7Info -t images/your-binary.bin
   # This will create a your-binary_template.cfg file in ME7Logger/logs
   ```
4. Modify the `.ecu` file to change connection from `SLOW-0x11` to `SLOW-0x00`  
    and `LogSpeed` from `56000` to `125000`
    ```sh
    sed -i 's\SLOW-0x11\SLOW-0x00\g' ecus/your-binary.ecu
    sed -i 's\LogSpeed     = 56000\LogSpeed     = 125000\g' ecus/your-binary.ecu
    ```
5. Modify the `.cfg` file to change the `SamplesPerSecond` to `50`  
    ```sh
    sed -i 's\SLOW-0x11\SLOW-0x00\g' ecus/your-binary.ecu
    sed -i 's\LogSpeed     = 56000\LogSpeed     = 125000\g' ecus/your-binary.ecu
    ```
6. Note down which port the cable is using  
    6.1. *Windows*: Check `Device Manager` which `COM` port ir listed for device  
    6.2. *Linux*:
        ```sh
        sudo dmesg | grep 'ch341-uart\|tty'
        ```
## Log