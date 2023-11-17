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
        2.2.3. `make && sudo make install # Debian`

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
    sed -i 's\SamplesPerSecond   = 20\SamplesPerSecond   = 50\g' logs/your-binary_template.cfg
    ```
6. Manually edit the `.cfg` file and uncomment which sensors you will be logging  
    ```sh
    vim logs/your-binary_template.cfg
    # delete ';' in front of the sensor name
    ```
7. Note down which port the cable is using  
    7.1. *Windows*: Check `Device Manager` which `COM` port ir listed for device (`example: COM4`)  
    7.2. *Linux*: `sudo dmesg | grep 'ch341-uart\|tty' # example: ttyUSB0`

## Log

1. Plug in the CH34* cable into the `OBD2` port
2. Turn the ignition `ON`
3. **DO NOT START YOUR VEHICLE YET!** `# note: if you lose connection you will have to shut off the engine to reconnect`
4. Start the logger:  
    ```sh
    cd /path/to/ME7Logger
    ./bin/ME7Logger -p <COM4/ttyUSB0> your-binary_template.cfg
    # -p should match the port you found in previous step 6
    # your .cfg file should match on what you had generated in step 3
    # previous steps 4.2 and 5 can be skipped by passing those values with -b 125000 and -s 50
    ```  
5. Once you see `-> Start logging (logdatat size=39, 50...) oooOoOOooooOOOo` it means you're logging.

That's it. After a run hit `CTRL+C` to stop the logger and locate your log in `/path/to/ME7Logger/logs/<name>_<date>_<time>.csv`

## Notes

 - Tested and confirmed working with "KKL VAG-COM for 409.1" cable that has `CH340` chip inside on Windows 10 64-bit system and Arch Linux as well.
 - These settings can also be applied in `VisualME7Logger` in the `Options` tab
