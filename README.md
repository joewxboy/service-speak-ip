# service-speak-ip

![MIT License](https://img.shields.io/github/license/open-horizon-services/service-speak-ip?label=License&color=blue) ![AMD86](https://img.shields.io/badge/x86-yes-green) ![ARM64](https://img.shields.io/badge/arm64-yes-green) ![Contributors](https://img.shields.io/github/contributors/open-horizon-services/service-speak-ip.svg)

## Description

`service-speak-ip` is a lightweight Open Horizon microservice designed to audibly announce the LAN IP address of its host device (such as a Raspberry Pi) every 30 seconds. It uses the `espeak` tool to convert the IP address to speech, making it easy to identify the device's network address without needing a display or SSH access. This service is ideal for IoT and edge computing scenarios where quick, hands-free identification of a device's IP is useful.

It does this over and over forever, every 30 seconds.

Of course, if you wish to hear the speech, you will need to plug in some earphones/headphones or if you have configured your Raspberry Pi to use only the HDMI for audio output, then you will need to connect an HDMI cable to something that can emit audio.

This little example uses the default English `espeak` voice for this (which is pretty horrible), and sends it a text string that looks similar to this:

```
Your eye pee address is 1 9 2 dot 1 6 8 dot 1 dot 1 2 3
```

The `espeak` tool can pronounce many other languages, so hack away if you want that. Here is some documentation:
   [http://espeak.sourceforge.net/docindex.html](http://espeak.sourceforge.net/docindex.html)

By default it only looks at the `eth0` interface. If yu plan to use WiFi then edit the Makefile to specify `wlan0` as the `INTERFACE_NAME`. If deploying wiith Open Horizon, set the `INTERFACE_NAME` as a Service variable.

Of course, specifying the `INTERFACE_NAME` is inconvenient. You could do something better, like determine the hosts's "default route" and then find the host interface that was on that network, and then announce the IP address of that interface. If you did that, then then you would not need to pass any `INTERFACE_NAME` at all. But I was tooo lazy to code that! :-)

This has currently only been tested on a few Raspberry Pi models.

## Prerequisites

- **Hardware:** Raspberry Pi (or compatible Linux device) with audio output (headphones, speakers, or HDMI audio)
- **Software:**
  - Docker (for containerized deployment)
  - Python 3
  - `espeak` (speech synthesis)
  - `netifaces` Python package
- **Environment Variables:**
  - `INTERFACE_NAME` (default: `eth0`) — The network interface whose IP address will be spoken. Set to `wlan0` for WiFi, or another interface as needed.

## Installation

### Docker (Recommended)

1. Clone this repository:
   ```sh
   git clone https://github.com/open-horizon-services/service-speak-ip.git
   cd service-speak-ip
   ```
2. Build the Docker image:
   ```sh
   docker build -t speak-ip:latest .
   ```

### Local (Direct Python)

1. Install dependencies:
   ```sh
   sudo apt-get update && sudo apt-get install -y python3 python3-pip espeak
   pip3 install netifaces
   ```
2. (Optional) Set the `INTERFACE_NAME` environment variable:
   ```sh
   export INTERFACE_NAME=eth0  # or wlan0, etc.
   ```

## Usage

### Docker

Run the service in a container (replace `eth0` with your interface if needed):

```sh
docker run -d \
  --name speak-ip \
  --restart unless-stopped \
  --device /dev/snd \
  --net=host \
  -e INTERFACE_NAME=eth0 \
  speak-ip:latest
```

### Local (Direct Python)

Run the service directly (ensure dependencies are installed):

```sh
python3 speak-ip.py
```

### Testing

- Attach a speaker or headphones to your device.
- The service will announce the IP address every 30 seconds.
- To change the interface, set the `INTERFACE_NAME` environment variable before running.

## Advanced Details

### Environment Variables
- `INTERFACE_NAME`: The network interface to monitor (default: `eth0`). Set to `wlan0` for WiFi or another interface as needed.

### Customization
- The service uses the default English `espeak` voice. You can modify `speak-ip.py` to use a different language or voice supported by `espeak`.
- By default, the service only looks at the specified interface. For more advanced detection (e.g., auto-detecting the default route), you can enhance the script as described in the comments.

### How It Works
- The service retrieves the IPv4 address of the specified interface using the `netifaces` Python package.
- It converts the IP address into a "speakable" string (e.g., `1 9 2 dot 1 6 8 dot 1 dot 1 2 3`).
- The `espeak` tool is used to audibly announce the IP address every 30 seconds.

## Authors

| Name         | GitHub      | Email                |
|--------------|-------------|----------------------|
| Troy Fine    | [t-fine](https://github.com/t-fine)         | <troy.fine@ibm.com>      |
| John Walicki | [johnwalicki](https://github.com/johnwalicki) | <walicki@us.ibm.com>     |

