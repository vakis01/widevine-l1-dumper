# Widevine L1 Dumper

A comprehensive tool for extracting Widevine L1 DRM credentials from compatible Android devices. This tool enables security researchers and developers to analyze Widevine implementation on devices that support L1 certification.

## Overview

Widevine is Google's industry-standard Digital Rights Management (DRM) solution. L1 is the highest security level, implementing hardware-backed key storage. This dumper extracts cryptographic material and device identifiers from L1-certified devices for research and analysis purposes.

### Features

- **Device Detection**: Automatically identifies Widevine L1 capable devices
- **Credential Extraction**: Securely extracts device keys and certificates
- **Multi-Device Support**: Works with various Android manufacturers and models
- **Secure Extraction**: Implements best practices for credential handling
- **Detailed Logging**: Comprehensive logging for debugging and analysis
- **Export Formats**: Multiple output formats (JSON, binary, PEM)

## Requirements

- **Android Device**: With Widevine L1 certification
- **Python 3.8+**: Core runtime environment
- **USB Debugging**: Enabled on target device
- **ADB**: Android Debug Bridge (included in Android SDK)
- **Root Access** (optional): Required for certain extraction methods

## Installation

### From Source

```bash
git clone https://github.com/vakis01/widevine-l1-dumper.git
cd widevine-l1-dumper
pip install -r requirements.txt
```

### Docker

```bash
docker build -t widevine-dumper .
docker run --device=/dev/bus/usb -it widevine-dumper
```

## Quick Start

### 1. Connect Your Device

```bash
adb devices
adb connect <device-ip>  # For network debugging
```

### 2. Run the Dumper

```bash
python -m widevine_dumper --device <device-id> --output ./dumps
```

### 3. Verify Output

```bash
ls -la ./dumps/
python -m widevine_dumper.tools.verify ./dumps/
```

## Usage

### Basic Extraction

```bash
python -m widevine_dumper extract \
  --device <device-id> \
  --output credentials.json
```

### Advanced Options

```bash
python -m widevine_dumper extract \
  --device <device-id> \
  --output credentials.json \
  --format json \
  --include-certificates \
  --include-keys \
  --verbose
```

### List Supported Devices

```bash
python -m widevine_dumper list-devices
```

### Verify Device Compatibility

```bash
python -m widevine_dumper check <device-id>
```

## Output Formats

### JSON Format
```json
{
  "device_id": "...",
  "device_name": "...",
  "widevine_version": "...",
  "security_level": "L1",
  "certificates": [...],
  "keys": {...}
}
```

### Binary Format
Raw cryptographic material in binary format for analysis tools.

### PEM Format
Standard PEM encoding for certificates and keys.

## Architecture

```
src/
  __init__.py
  main.py                    Entry point
  cli.py                     Command-line interface
  
  device/
    __init__.py
    manager.py               Device detection and management
    detector.py              Widevine L1 detection
    adb.py                   ADB communication wrapper
    
  extraction/
    __init__.py
    extractor.py             Main extraction logic
    methods/                 Different extraction techniques
      hwkey.py               Hardware key extraction
      certificate.py         Certificate extraction
      keybox.py              Keybox file extraction
      
  crypto/
    __init__.py
    widevine.py              Widevine-specific cryptography
    keywrap.py               Key wrapping/unwrapping
    parser.py                Binary format parsing
    
  exporters/
    __init__.py
    json_exporter.py         JSON output
    pem_exporter.py          PEM output
    binary_exporter.py       Binary output
    
  utils/
    __init__.py
    logger.py                Logging utilities
    validators.py            Input validation
    device_info.py           Device information utilities

tests/
  __init__.py
  conftest.py                pytest configuration
  unit/                      Unit tests
  integration/               Integration tests
  fixtures/                  Test data

docs/
  ARCHITECTURE.md            Design documentation
  API.md                     API reference
  DEVICE_SUPPORT.md          Supported devices list
  TROUBLESHOOTING.md         Common issues and solutions
  
examples/
  basic_extraction.py        Simple usage example
  batch_dump.py              Batch device processing
  custom_exporter.py         Custom export format
```

## Security Considerations

⚠️ **WARNING**: This tool extracts sensitive DRM credentials. Use responsibly:

- Only use on devices you own or have explicit permission to analyze
- Keep extracted credentials secure and confidential
- Do not distribute credentials without authorization
- Be aware of legal implications in your jurisdiction
- Comply with DMCA and similar legislation

## Configuration

Create a `.widevine-config.yaml` file:

```yaml
# Device configuration
device:
  timeout: 30
  retry_attempts: 3
  
# Extraction settings
extraction:
  method: "auto"  # auto, hwkey, certificate, keybox
  include_certificates: true
  include_keys: true
  
# Output options
output:
  format: "json"  # json, binary, pem
  compression: true
  
# Logging
logging:
  level: "INFO"
  file: "widevine_dumper.log"
```

## Supported Devices

See [DEVICE_SUPPORT.md](docs/DEVICE_SUPPORT.md) for a comprehensive list of tested and compatible devices.

### Quick Device Check

```bash
python -m widevine_dumper list-devices --l1-only
```

## Troubleshooting

### Device Not Detected

```bash
# Enable USB debugging
adb shell settings put global adb_enabled 1

# Reconnect
adb disconnect
adb connect <device-ip>
```

### Extraction Fails

```bash
# Enable verbose logging
python -m widevine_dumper extract --device <id> --verbose

# Check device compatibility
python -m widevine_dumper check <device-id> --verbose
```

See [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md) for detailed solutions.

## Development

### Setup Development Environment

```bash
pip install -r requirements-dev.txt
pre-commit install
```

### Run Tests

```bash
pytest tests/ -v
pytest tests/ -v --cov=src
```

### Build Documentation

```bash
cd docs
make html
```

## API Usage

```python
from widevine_dumper.device import DeviceManager
from widevine_dumper.extraction import Extractor

# Initialize device manager
device_mgr = DeviceManager()
devices = device_mgr.list_devices()

# Extract credentials
extractor = Extractor(devices[0])
credentials = extractor.extract()

# Export results
from widevine_dumper.exporters import JSONExporter
exporter = JSONExporter()
exporter.export(credentials, "output.json")
```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

## Testing

```bash
# Unit tests
pytest tests/unit/

# Integration tests (requires connected device)
pytest tests/integration/

# All tests with coverage
pytest --cov=src tests/
```

## Documentation

- [Architecture](docs/ARCHITECTURE.md) - System design and component overview
- [API Reference](docs/API.md) - Detailed API documentation
- [Device Support](docs/DEVICE_SUPPORT.md) - Compatible devices
- [Troubleshooting](docs/TROUBLESHOOTING.md) - Common issues
- [Contributing](CONTRIBUTING.md) - Contribution guidelines

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## Disclaimer

This tool is provided for educational and research purposes only. Users are responsible for complying with all applicable laws and regulations. The authors are not liable for misuse or unauthorized access to DRM-protected content.

## Citation

If you use this tool in research, please cite:

```bibtex
@software{widevine_l1_dumper,
  author = {Vakis01},
  title = {Widevine L1 Dumper},
  url = {https://github.com/vakis01/widevine-l1-dumper},
  year = {2024}
}
```

## Support

- **Issues**: [GitHub Issues](https://github.com/vakis01/widevine-l1-dumper/issues)
- **Discussions**: [GitHub Discussions](https://github.com/vakis01/widevine-l1-dumper/discussions)
- **Security**: See [SECURITY.md](SECURITY.md) for reporting security vulnerabilities

## Resources

- [Widevine Overview](https://www.widevine.com/)
- [Android DRM Framework](https://developer.android.com/guide/topics/media/drm)
- [Keybox Format](https://github.com/google/android-test-kit/wiki/Keybox)
- [Hardware Key Storage](https://source.android.com/security/keystore)
