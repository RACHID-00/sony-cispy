# sony-cispy

[![Python 3.13+](https://img.shields.io/badge/python-3.13+-blue.svg)](https://www.python.org/downloads/)

A modern, robust Python library for controlling Sony Audio/Video Receivers (AVRs) and Soundbars that support the CIS-IP2 protocol over Ethernet/IP.

**CIS stands for Custom Installation Services (or Solutions).**

_It's the **Sony Specific** protocol companies like Crestron, Control4 and homeseer use to provide their customers with local, granular control of their Sony AV devices._

## Overview

`sony-cispy` provides a clean, async/await-based interface for communicating with Sony AVRs and soundbars using the CIS-IP2 protocol. This library merges the best aspects of previous implementations, offering:

- **Universal API** - Works with any CIS-IP2 compatible device
- **Robust Connection Handling** - Command ID tracking with futures for reliable request/response matching
- **Real-time Notifications** - Receive instant updates when device state changes
- **Full Protocol Support** - Access to the complete CIS-IP2 command set

## Features

- ✨ **Universal API**: Generic `get_feature()` and `set_feature()` methods that work with any CIS-IP2 feature
- 🔄 **Robust Connection Handling**: Command ID tracking with futures for reliable request/response matching
- 📡 **Real-time Notifications**: Register callbacks to receive real-time updates when device state changes
- 🔀 **JSON Stream Decoding**: Handles multiple JSON messages arriving in a single read
- ⏱️ **Timeout Handling**: Configurable timeouts for all network operations
- 📚 **Command Discovery**: Access to the full CIS-IP2 command set via `commands_dict`
- 🚀 **Modern Async/Await**: Built with Python 3.13+ async/await patterns
- 🧪 **Well Tested**: Comprehensive unit test suite with mocked network connections

## About the Protocol

The Sony CIS-IP2 protocol is a network (Ethernet/IP) command language for controlling Sony Audio/Video Receivers (AVRs), Soundbars and potentially other Sony AV devices. This protocol allows you to control volume, settings, and inputs over the network, rather than using older serial (RS-232) methods.


**Important Notes:**
- Sony does not officially refer to this as "CIS-IP2" in their documentation or device settings
- This protocol is typically enabled through settings like "External Control", "Simple IP Control" or "IP Control" in your AVR/soundbar's menu
- The exact setting name may vary by device model (e.g., "Network Control", "IP Remote", etc.)


### Checking Device Compatibility

Before using this library, you should verify that your device supports this protocol and that it's enabled. The easiest way to test compatibility is using `netcat`:

```bash
netcat <IP_ADDRESS> 33336
{"id":3, "type":"get","feature":"main.power"}
```

Press Enter after typing the JSON command. You should receive a JSON response with the power state (e.g., `{"id":3,"type":"result","feature":"main.power","value":"on"}`).

**If you don't get a response:**
1. Check that your device is on the same network
2. Verify the IP address is correct
3. Ensure IP/Network control is enabled in your device's settings
4. Check that port 33336 is not blocked by a firewall
5. Some devices may require specific firmware versions to support this protocol


## Installation

### From Source

```bash
# Clone the repository (or download and extract)
cd sony-cispy

# Install in development mode
pip install -e .
```

Or install directly from a local path:

```bash
pip install -e /path/to/sony-cispy
```

### Development Installation

```bash
# Clone the repository (or download and extract)
cd sony-cispy

# Install development dependencies
pip install -r requirements-dev.txt

# Install in development mode
pip install -e .
```

## Quick Start

### Basic Usage

```python
import asyncio
from sony_cispy import SonyCISIP2

async def main():
    # Create client
    client = SonyCISIP2(host="192.168.1.100", port=33336)
    
    # Connect
    await client.connect()
    
    try:
        # Get power state
        power = await client.get_feature("main.power")
        print(f"Power: {power}")
        
        # Set volume
        result = await client.set_feature("main.volumestep", 50)
        print(f"Volume set: {result}")
        
        # Get volume
        volume = await client.get_feature("main.volumestep")
        print(f"Current volume: {volume}")
        
    finally:
        # Disconnect
        await client.disconnect()

asyncio.run(main())
```

### Using Context Manager (Recommended)

```python
import asyncio
from sony_cispy import SonyCISIP2

async def main():
    async with SonyCISIP2(host="192.168.1.100") as client:
        power = await client.get_feature("main.power")
        print(f"Power: {power}")
        
        await client.set_feature("main.power", "on")
        await client.set_feature("main.volumestep", 50)

asyncio.run(main())
```

## Real-time Notifications

Register callbacks to receive real-time notifications when device state changes:

```python
import asyncio
from sony_cispy import SonyCISIP2

async def on_power_change(feature, value):
    print(f"⚡ Power changed to: {value}")

async def on_volume_change(feature, value):
    print(f"🔊 Volume changed to: {value}")

async def on_any_change(feature, value):
    print(f"📢 {feature} changed to {value}")

async def main():
    client = SonyCISIP2(host="192.168.1.100")
    await client.connect()
    
    try:
        # Register callbacks
        client.register_notification_callback("main.power", on_power_change)
        client.register_notification_callback("main.volumestep", on_volume_change)
        client.register_notification_callback(None, on_any_change)  # All notifications
        
        # Keep running to receive notifications
        await asyncio.sleep(60)
        
    finally:
        await client.disconnect()

asyncio.run(main())
```

## Command Discovery

Discover available CIS-IP2 commands programmatically:

```python
from sony_cispy import commands_dict

# List all available commands
for feature, details in commands_dict.items():
    print(f"{feature}: {details['description']}")
    print(f"  Set: {details['set']}, Get: {details['get']}, Notify: {details['notify']}")
```

## Common Features

### Power Control
```python
# Get power state
power = await client.get_feature("main.power")  # Returns "on" or "off"

# Set power
await client.set_feature("main.power", "on")
await client.set_feature("main.power", "off")
await client.set_feature("main.power", "toggle")
```

### Volume Control
```python
# Get/set volume (0-100)
volume = await client.get_feature("main.volumestep")
await client.set_feature("main.volumestep", 50)

# Get/set volume in dB (-92.0 to 23.0)
volume_db = await client.get_feature("main.volumedb")
await client.set_feature("main.volumedb", -20.0)

# Mute
mute = await client.get_feature("main.mute")
await client.set_feature("main.mute", "on")
```

### Input Selection
```python
# Get current input
input_source = await client.get_feature("main.input")

# Set input (values vary by device)
await client.set_feature("main.input", "tv")
await client.set_feature("main.input", "hdmi1")
await client.set_feature("main.input", "spotify")
```

### Audio Settings
```python
# Sound field
await client.set_feature("audio.soundfield", "on")

# Voice enhancer
await client.set_feature("audio.voiceenhancer", "upon")
```

## Requirements

- **Python**: 3.13 or higher
- **Dependencies**: None (uses only standard library)

## Documentation

- [Library Documentation](sony_cispy/README.md) - Detailed API documentation
- [Merge Summary](MERGE_SUMMARY.md) - Details about the library merge
- [Test Suite](TEST_SUMMARY.md) - Information about the test suite
- [Examples](example.py) - More usage examples

## Project Structure

```
sony-cispy/
├── sony_cispy/          # Main library package
│   ├── __init__.py
│   ├── client.py        # SonyCISIP2 client class
│   ├── constants.py     # Protocol constants
│   ├── commandset.py    # Full CIS-IP2 command dictionary
│   └── variables.py     # Variable placeholders
├── tests/               # Unit tests
│   ├── test_client.py
│   ├── test_constants.py
│   └── ...
├── example.py           # Usage examples
└── README.md            # This file
```

## Development

### Running Tests

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run all tests
pytest

# Run with coverage
pytest --cov=sony_cispy --cov-report=html

# Run specific test file
pytest tests/test_client.py
```

### Code Quality

```bash
# Format code
black sony_cispy/ tests/

# Lint code
ruff check sony_cispy/ tests/

# Type checking
mypy sony_cispy/
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Follow PEP 8 style guidelines
- Add tests for new features
- Update documentation as needed
- Ensure all tests pass before submitting

## License

This project is provided as-is. See individual source files for license information.

## Disclaimer

This library is not officially supported by Sony. Use at your own risk. The CIS-IP2 protocol may vary between device models, and not all commands may work with all devices.

## Acknowledgments

This library was created by merging the best aspects of:
- The original `python_sonycisip2` library's universal API
- The `bravia-quad-homeassistant` integration's robust connection handling, _with special thanks to @liudger for his code contributions!_

See [MERGE_SUMMARY.md](MERGE_SUMMARY.md) for more details.

## Support

- 🐛 **Bug Reports**: Open an issue in the repository
- 💡 **Feature Requests**: Open an issue in the repository
- 📖 **Documentation**: See the [library documentation](sony_cispy/README.md)
- 💬 **Questions**: Open a discussion in the repository

---

**Made with ❤️ for the home automation and audio enthusiast community**

