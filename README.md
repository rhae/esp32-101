# esp32-101
ESP32-WROOM-32 board with `arduino-cli`.

## Goal
I had a ESP32 board lying around and the `arduino-cli` is new and I haven't used it.
This project is my playground to setup an `arduino-cli` project on linux.

## Board

The board datasheet [ESP32 WROOM C2](docs/ESP-32%20Dev%20Kit%20C%20V2_EN.pdf)

## Linux setup

The board was immediately detected and a character device /dev/ttyUSB0 was
created from Ubuntu.

In case it is not automagically detected a new udev rule needs to be added.
The board uses a `Silicon Labs CP210x UART Bridge` with the USB VID/PID 10c4/ea60.
Add a text file in `/etc/udev/rules.d` with the name `99-arduino.rules`.

```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", GROUP="plugdev", MODE="0666", TAG="uaccess"
```

## Install Arduino-cli

### Automated variant

```bash copy
curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh
```

### Manual variant
Download arduino-cli from [Arduino Github](https://github.com/arduino/arduino-cli/releases).

In my case I used the file `arduino-cli_1.1.1_Linux_64bit.tar.gz`
Then extract it and prepare the shell to find it in the `PATH` variable.
I also setup an alias to ease the use in typing.

```bash copy
export FILE=arduino-cli_1.1.1_Linux_64bit.tar.gz
mkdir -p ~/.local/bin
cd ~/.local/bin
tar zxf ~/Downloads/$FILE
cd ~
```

### Accomodate for ease of use on command line

Add the path to your `PATH` environment variable.
To do this:
- open the file `~/.bash.rc`
- scroll to the end of the file
- add the following lines:

```bash copy

### Setup Arduino-cli

export PATH=$PATH:$HOME/.local/bin

alias ac=arduino-cli
```

## Setup esp32

I did not have to set an extra board manager.
The arduino-cli seems to know the board esp32:esp32 by its own already.
I decided to use the esp32:esp32 since it think it is more up-to-date than
the arduino:esp32 core package.

```bash copy
arduino-cli core update-index
arduinoc core install esp32
```

## Setup sketch

Arduino-cli uses a sketch.yaml file to describe the setup.

```yaml copy
profiles:
  azdelivery:
    fqbn: esp32:esp32:esp32
    notes: AZ-DElivery Dev Moduls C2, USB/VID 10c4/ea60
    programmer: esptool
    port: /dev/ttyUSB0
    port_config:
        baudrate: 115200
    platforms:
      - platform: esp32:esp32 (3.0.7)

default_profile: azdelivery
```
