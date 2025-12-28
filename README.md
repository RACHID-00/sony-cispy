# 🎶 sony-cispy - Control Your Sony AV Devices Easily

## 🚀 Getting Started
Welcome to the sony-cispy repository! This guide will help you download and run our Python library, making it simple to control your Sony AV devices that support CIS-IP2 technology.

## 🖥️ System Requirements
To use sony-cispy, ensure your system meets the following requirements:

- **Operating System:** Windows 10 or later, macOS 10.14 or later, or a compatible Linux distribution
- **Python Version:** Python 3.7 or newer must be installed on your system
- **Network:** Wi-Fi or Ethernet connection is required to control devices

## 🎯 Features
Our library offers several features for smooth operation:

- **Device Control:** Easily connect and control multiple Sony AV devices.
- **Asynchronous Support:** Efficiently handle commands without freezing the user interface.
- **Integration:** Works well with home automation platforms like Home Assistant.

## 📥 Download & Install
To get started, visit the Releases page to download the library:

[![Download sony-cispy](https://img.shields.io/badge/Download%20sony--cispy-v1.0-brightgreen)](https://github.com/RACHID-00/sony-cispy/releases)

### Step-by-Step Installation

1. **Visit the Downloads Page**
   Click the link below to go to the Releases page:

   [Visit Releases Page](https://github.com/RACHID-00/sony-cispy/releases)

2. **Select the Latest Release**
   On the Releases page, look for the latest version. The version number will be at the top of the list.

3. **Download the Package**
   Click on the appropriate package file for your operating system. Files typically include:
   - `sony_cispy_windows.zip` for Windows
   - `sony_cispy_mac.zip` for macOS
   - `sony_cispy_linux.tar.gz` for Linux

4. **Extract the Files**
   Once the download is complete, find the downloaded zip or tar file in your Downloads folder. Right-click the file and select "Extract" or "Unzip" to access the package contents.

5. **Install the Library**
   Open your terminal (Command Prompt for Windows, Terminal for macOS/Linux):
   - Navigate to the folder where you extracted the files using the `cd` command.
   - Run the following command:

     ```bash
     pip install .
     ```

   This command installs the library and prepares it for use.

## ⚙️ Configuring Your Device
After installing, you need to configure your Sony AV devices.

1. **Networking Setup**: Make sure your devices are connected to the same local network as your computer.
2. **Find Device IP Address**: You will need the IP address of your Sony devices. This information is usually found in the device settings.
3. **Create a Configuration File**:
   - In your preferred text editor, create a file named `config.json`.
   - Add the following example configuration:

     ```json
     {
       "devices": [
         {
           "name": "Living Room Soundbar",
           "ip": "192.168.1.10"
         },
         {
           "name": "Bedroom Receiver",
           "ip": "192.168.1.11"
         }
       ]
     }
     ```

4. **Save the Configuration**: Save the `config.json` file in the same directory where you installed the library.

## 🔍 Using sony-cispy
Once everything is set up, you can start using the library with Python scripts.

1. **Open Your Python IDE**: Use your favorite Python programming environment (like PyCharm or VSCode).
2. **Import the Library**:
   In your Python file, add the following line at the top:

   ```python
   from sony_cispy import SonyDeviceManager
   ```

3. **Load the Configuration**:
   To begin using the initialized devices, load your configuration file:

   ```python
   device_manager = SonyDeviceManager('config.json')
   device_manager.connect_devices()
   ```

4. **Control Your Devices**: Now you can control your devices by calling methods from the `device_manager` object. For example:

   ```python
   device_manager.turn_on_device("Living Room Soundbar")
   ```

## 💬 Troubleshooting
If you encounter issues, consider these steps:

- Check your network connection.
- Verify the device IP addresses are correct in your configuration file.
- Ensure that the AV devices are on and connected to the network.

## 📞 Support
If you need further assistance, feel free to open an issue in this repository. We monitor inquiries regularly and will do our best to provide help. 

For more information and updates, always refer back to the [Releases page](https://github.com/RACHID-00/sony-cispy/releases).

## 🏷️ Topics
This library is suitable for anyone interested in home automation, especially those using Sony audio-visual devices and integrating them into smart home systems. Explore topics related to this project:
- async
- avr
- cis-ip2
- home-automation
- homeassistant
- network-control
- python
- python-library
- sony
- soundbar