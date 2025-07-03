# Orange Pi Mower Build System

This repository contains a customized Orange Pi build system with integrated mower-specific packages and configurations. It supports automated builds through GitHub Actions.

## Features

- **Automated CI/CD**: GitHub Actions workflow for building Orange Pi images
- **Mower Integration**: Pre-configured with ROS Noetic, development tools, and mower-specific packages
- **Multi-board Support**: Supports various Orange Pi boards (5+, 5, 4-LTS, 3-LTS, PC)
- **Flexible Configuration**: Easy to customize for different use cases
- **Docker Support**: Containerized builds for consistency

## Quick Start

### Using GitHub Actions (Recommended)

1. **Fork this repository** to your GitHub account

2. **Trigger a build** in one of these ways:
   - **Manual Trigger**: Go to Actions → "Build Orange Pi Mower Image" → "Run workflow"
   - **Automatic Trigger**: Push changes to `main` branch or create a pull request
   - **Release Build**: Create a git tag (e.g., `v1.0.0`) to trigger a release build

3. **Configure your build** using the workflow inputs:
   - `board`: Choose your Orange Pi board (default: orangepi5plus)
   - `branch`: Kernel branch (current/legacy/edge)
   - `release`: OS release (focal/jammy/bullseye/bookworm)
   - `build_desktop`: Include desktop environment (yes/no)
   - `enable_mower`: Enable mower packages (yes/no)

4. **Download artifacts** from the Actions page or releases

### Local Build

```bash
# Clone the repository
git clone https://github.com/yourusername/orangepi-build-mower.git
cd orangepi-build-mower

# Build using Docker (recommended)
./build.sh docker BOARD=orangepi5plus BRANCH=current RELEASE=focal BUILD_DESKTOP=no

# Or build natively (requires Ubuntu 20.04/22.04)
sudo ./build.sh BOARD=orangepi5plus BRANCH=current RELEASE=focal BUILD_DESKTOP=no
```

## Mower Configuration

The mower configuration includes:

### Software Packages
- **ROS Noetic**: Complete ROS installation with development tools
- **Development Tools**: GCC, CMake, Python, debugging tools
- **Libraries**: 
  - Abseil C++ (compiled from source)
  - CasADi C++ (compiled from source)
  - OpenCV, PCL, Eigen, Boost
  - CGAL, GSL, Protobuf

### System Configuration
- **Mower User**: Pre-configured user account with ROS environment
- **Workspace**: ROS workspace at `/opt/mower_ws`
- **CAN Interface**: Pre-configured CAN bus support
- **Development Environment**: Complete C++/Python development setup

### Hardware Support
- GPIO, SPI, I2C access
- CAN bus support
- Serial communication
- Hardware monitoring

## Supported Boards

| Board | Status | Notes |
|-------|--------|-------|
| Orange Pi 5+ | ✅ Tested | Recommended for mower projects |
| Orange Pi 5 | ✅ Tested | Good performance |
| Orange Pi 4 LTS | ✅ Tested | Stable platform |
| Orange Pi 3 LTS | ✅ Compatible | Older but supported |
| Orange Pi PC | ✅ Compatible | Budget option |

## Build Options

### GitHub Actions Parameters

| Parameter | Options | Default | Description |
|-----------|---------|---------|-------------|
| `board` | orangepi5plus, orangepi5, orangepi4-lts, orangepi3-lts, orangepipc | orangepi5plus | Target board |
| `branch` | current, legacy, edge | current | Kernel branch |
| `release` | focal, jammy, bullseye, bookworm | focal | OS release |
| `build_desktop` | yes, no | no | Include desktop environment |
| `enable_mower` | yes, no | yes | Enable mower packages |

### Build Artifacts

After a successful build, you'll get:
- `*.img.7z`: Compressed disk image
- `*.img.sha`: SHA256 checksum
- `build-info.txt`: Build metadata
- `logs/`: Build logs (if build fails)

## Configuration Files

### Template Customization
- `external/config/templates/customize-image.sh.template`: Main customization script
- Modify the `focal` section to add/remove packages

### User Patches
- `userpatches/`: User-specific configurations
- `userpatches/config-mower.conf`: Mower-specific build config
- `userpatches/overlay/`: Files to copy to the image

## GitHub Actions Workflow

The workflow automatically:
1. **Frees up disk space** (removes unused packages)
2. **Sets up Docker environment**
3. **Configures mower packages** (if enabled)
4. **Builds the image** using Docker
5. **Uploads artifacts** to GitHub
6. **Creates releases** (for tagged commits)

### Build Time
- **Typical build time**: 2-4 hours
- **Timeout**: 8 hours maximum
- **Parallel builds**: Not recommended (resource intensive)

### Resource Usage
- **Disk space**: ~20GB during build
- **Memory**: ~4GB minimum
- **CPU**: All available cores

## Installation

1. **Download** the `.7z` file from Actions artifacts or Releases
2. **Extract** the image:
   ```bash
   7z x Orange_Pi_5plus_focal_current_mower.img.7z
   ```
3. **Flash** to SD card (≥8GB):
   ```bash
   # Linux/macOS
   sudo dd if=Orange_Pi_5plus_focal_current_mower.img of=/dev/sdX bs=1M status=progress
   
   # Or use balenaEtcher for a GUI
   ```
4. **Boot** from SD card
5. **Login** with:
   - User: `mower` / Password: `mower123`
   - Root: `root` / Password: `orangepi`

## Development

### Adding Packages
1. Edit `external/config/templates/customize-image.sh.template`
2. Add packages to the `InstallMowerPackages` function
3. Commit and push to trigger a build

### Custom Configuration
1. Create `userpatches/config-myconfig.conf`
2. Add your build parameters
3. Build with `./build.sh myconfig`

### Testing Changes
1. Create a feature branch
2. Make your changes
3. Create a pull request
4. The workflow will build and test your changes

## Troubleshooting

### Build Failures
- Check the Actions logs for detailed error messages
- Common issues:
  - Disk space: Increase `FIXED_IMAGE_SIZE` in config
  - Network: Package download failures (retry usually works)
  - Permissions: Docker permission issues

### Image Issues
- **Boot failure**: Check if image was flashed correctly
- **Login issues**: Default passwords may have changed
- **Network**: Check if Ethernet cable is connected

### Getting Help
- Check the [Issues](https://github.com/yourusername/orangepi-build-mower/issues) page
- Create a new issue with:
  - Build logs
  - Configuration used
  - Error messages

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with a PR build
5. Submit a pull request

## License

This project is based on the Orange Pi build system and inherits its GPL-2.0 license.

## Acknowledgments

- Orange Pi build system by orangepi-xunlong
- Mower project integrations
- GitHub Actions community
