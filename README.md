# SmartUnlink

A standalone Windows application that broadcasts VITA-49 discovery packets for FlexRadio devices, enabling SmartSDR to discover radios over VPN connections where multicast doesn't work.

## The Problem

FlexRadio's SmartSDR uses multicast UDP to discover radios on the local network. When connecting over VPN, multicast packets don't traverse the VPN tunnel, making it impossible for SmartSDR to find your radio.

## The Solution

SmartUnlink broadcasts synthetic VITA-49 discovery packets on your local network (ports 4991 and 4992) that mimic what a real FlexRadio would send. SmartSDR receives these packets and displays the radio as if it were on the local network. You can then connect to the radio using its actual IP address over the VPN.

## Features

- Configure multiple FlexRadio devices
- Enable/disable broadcasting per radio
- Simple JSON configuration file
- Configurable broadcast interval (default: 3 seconds)
- Clean, modern UI
- Runs as a standalone Windows application

## Installation

### From Release (Recommended)

1. Download the latest release from the Releases page
2. Run the installer (`SmartUnlink-Setup-x.x.x.exe`) or use the portable version (`SmartUnlink-x.x.x-portable.exe`)

### From Source

```bash
# Clone the repository
git clone https://github.com/yourusername/SmartUnlink.git
cd SmartUnlink

# Install dependencies
npm install

# Run in development mode
npm start

# Build for Windows
npm run build
```

## Usage

1. **Add a Radio**: Click "Add Radio" and enter:
   - **Name**: A friendly name (e.g., "My Flex 6600")
   - **IP Address**: The radio's IP address accessible over VPN
   - **Model**: Select your FlexRadio model
   - **Serial Number**: Your radio's serial number
   - **Callsign**: (Optional) Your amateur radio callsign
   - **Firmware Version**: (Optional) Defaults to 4.1.3.39644

2. **Enable Broadcasting**: Toggle the switch on the radio card to start broadcasting

3. **Connect with SmartSDR**: Open SmartSDR and your radio should appear in the discovery list. Click to connect.

## Configuration

The configuration is stored in a JSON file:

- **Development**: `./config.json` (in the app directory)
- **Installed**: `%APPDATA%/smartunlink/config.json`

### Example Configuration

```json
{
  "broadcastIntervalMs": 3000,
  "radios": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "name": "My Flex 6600",
      "ipAddress": "10.0.1.100",
      "model": "FLEX-6600",
      "serialNumber": "1234-5678-9ABC-DEF0",
      "callsign": "W1ABC",
      "enabled": true,
      "version": "4.1.3.39644"
    }
  ]
}
```

### Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `broadcastIntervalMs` | number | 3000 | How often to broadcast discovery packets (ms) |
| `radios` | array | [] | List of configured radios |

### Radio Options

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `name` | string | Yes | Friendly name for the radio |
| `ipAddress` | string | Yes | IP address of the radio |
| `model` | string | Yes | FlexRadio model |
| `serialNumber` | string | Yes | Radio serial number |
| `callsign` | string | No | Your amateur callsign |
| `enabled` | boolean | No | Whether to broadcast for this radio |
| `version` | string | No | Firmware version (default: 4.1.3.39644) |

## Supported Models

- FLEX-5100
- FLEX-5200
- FLEX-6400
- FLEX-6400M
- FLEX-6600
- FLEX-6600M
- FLEX-6700
- FLEX-8400
- FLEX-8600

## Technical Details

### VITA-49 Discovery Packet

SmartUnlink generates properly formatted VITA-49 discovery packets that include:

- FlexRadio OUI (0x00001C2D)
- Discovery protocol version 3.1.0.2
- Radio model, serial, version, callsign
- IP address and port information
- Client availability information

### Network Requirements

- UDP broadcast capability on the local network
- Ports 4991 and 4992 must not be blocked by firewall
- SmartSDR must be running on the same subnet

### VPN Configuration

Your VPN must be configured to allow:
- TCP/UDP traffic to the radio's IP address
- SmartSDR default ports (4992 for command, 4991 for streaming)

## Troubleshooting

### Radio doesn't appear in SmartSDR

1. Ensure SmartUnlink is running and broadcasting (check status indicator)
2. Verify the radio is enabled (toggle switch is ON)
3. Check Windows Firewall isn't blocking UDP broadcasts
4. Ensure SmartSDR and SmartUnlink are on the same network interface

### Can't connect to radio

1. Verify VPN is connected and radio IP is reachable (`ping <radio-ip>`)
2. Check that the IP address configured matches the radio's VPN IP
3. Ensure no firewall is blocking ports 4991-4992

### Broadcast errors

Check the console (View > Toggle Developer Tools in dev mode) for detailed error messages.

## Development

```bash
# Install dependencies
npm install

# Run in development mode (with DevTools)
npm start

# Build Windows installer
npm run build

# Build portable executable only
npm run build:portable
```

## License

MIT License

## Credits

Based on the SmartUnlink implementation from [Log4YM](https://github.com/yourusername/Log4YM), adapted as a standalone application.
