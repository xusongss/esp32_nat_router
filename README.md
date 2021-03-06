# ESP32 NAT Router

This is a firmware to use the ESP32 as WiFi NAT router, based on the [Console Component](https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/console.html#console) and the [esp-idf-nat-example](https://github.com/jonask1337/esp-idf-nat-example). It can achieve a bandwidth of more than 15mbps.

## Usage

For configuration you have to use a serial console (Putty or GtkTerm with 115200 bps).
Use the "set_sta" and the "set_ap" command to configure the WiFi settings. Changes are stored persistently in NVS and are applied after next restart. Use "show" to display the current config.

Enter the `help` command get a full list of all available commands.

If you want to enter non-ASCII or special characters you can use HTTP-style hex encoding (e.g. "My%20AccessPoint" results in a string "My AccessPoint").

## Flashing the prebuild Binaries
Install [esptool](https://github.com/espressif/esptool), go to the project directory, and enter:
```
esptool.py --chip esp32 --port /dev/ttyUSB0 \
--baud 115200 --before default_reset --after hard_reset write_flash \
-z --flash_mode dio --flash_freq 40m --flash_size detect \
0x1000 build/bootloader/bootloader.bin \
0x10000 build/console.bin \
0x8000 build/partitions_example.bin
```

As an alternative you might use [Espressif's Flash Download Tools](https://www.espressif.com/en/products/hardware/esp32/resources) with the parameters given in the figure below (thanks to mahesh2000):

![image](https://user-images.githubusercontent.com/1047754/74902819-27bfad00-539f-11ea-86a5-36112eb52acf.png)

## Building the Binaries
The following are the steps required to compile this project:

### Step 1 - Setup ESP-IDF
Download and setup the ESP-IDF.

### Step 2 - Get the lwIP library with NAT
Download the repository of the NAT lwIP library using the follwing command:

`git clone https://github.com/martin-ger/esp-lwip.git`

Note: It is important to clone the repository. If it is only downloaded it will be replaced by the orginal lwIP library during the build.

### Step 3 - Replace original ESP-IDF lwIP library with NAT lwIP library
1. Go to the folder where the ESP-IDF is installed.
2. Rename or delete the *esp-idf/component/lwip/lwip* directory.
3. Move the lwIP library with NAT repository folder from Step 2 to *esp-idf/component/lwip/*
4. Rename the lwIP library with NAT repository folder from *esp-lwip* to *lwip*.

### Step 4 - Build and flash the esp-idf-nat-example project
1. Configure and set the option "Enable copy between Layer2 and Layer3 packets" in the ESP-IDF project configuration.
    1. In the project directory run `make menuconfig` (or `idf.py menuconfig` for cmake).
    2. Go to *Component config -> LWIP* and set "Enable copy between Layer2 and Layer3 packets" option.
2. Build the project and flash it to the ESP32.

A detailed instruction on how to build, configure and flash a ESP-IDF project can also be found the official ESP-IDF guide.

### DNS
By Default the DNS-Server which is offerd to clients connecting to the ESP32 AP is set to 8.8.8.8.
Replace the value of the *MY_DNS_IP_ADDR* with your desired DNS-Server IP address (in hex) if you want to use a different one.

## Troubleshooting

### Line Endings

The line endings in the Console Example are configured to match particular serial monitors. Therefore, if the following log output appears, consider using a different serial monitor (e.g. Putty for Windows or GtkTerm on Linux) or modify the example's UART configuration.

```
This is an example of ESP-IDF console component.
Type 'help' to get the list of commands.
Use UP/DOWN arrows to navigate through command history.
Press TAB when typing command name to auto-complete.
Your terminal application does not support escape sequences.
Line editing and history features are disabled.
On Windows, try using Putty instead.
esp32>
```
