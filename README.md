# stm32-ground

## Prerequisites

This project requires the following tools to build STM32 firmware:

### Build Tools (Installed)
- **ARM Embedded GCC Toolchain** (gcc-arm-none-eabi 13.2.1) - ARM compiler for embedded systems
- **CMake** (3.28.3) - Build system generator
- **GNU Make** (4.3) - Build automation tool

To install these tools on Ubuntu/Debian:
```bash
sudo apt install -y gcc-arm-none-eabi cmake make
```

### Additional Components
- **binutils-arm-none-eabi** - Binary utilities for ARM targets
- **libnewlib-arm-none-eabi** - C standard library for embedded systems
- **libstdc++-arm-none-eabi** - C++ standard library for ARM targets

