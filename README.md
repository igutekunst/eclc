# Embedded C++ Library Collection


The Embedded C++ Library Collection is a collection of loosely coupled C++ libraries designed to be optimal for
developing firmware for resource constrained embedded systems.

ECLC aims to be all the "batteries" necessary for a "batteries included" programming environment for embedded systems.

## Notice
This README is aspirational. It is written in present tense as if the work has been completed. As development commences, the language will be updated to reflect the current status, and the aspirational statements will be moved to another document.

## Components

### Standard Software Library
 - List
 - Map
 - Lazy Map and List
 - Queue
 - Task/Threads
 - Timers
 - Time API
 - Mutex
 - String manipulation

### Drivers
ECLC provides a driver framework and drivers for a variety of standard interfaces and hardware peripherals. 

#### On Chip Drivers
 - UART/USART
 - SPI and QSPI
 - I2C
 - GPIO
 - PWM
 - CAN
 - Ethernet
 - RTC
 - ADC
 - DAC
 - Cryptographic Accelerators
 - DMA
 - Block Storage

#### Off-Chip/high level drivers
ECLC also provides drivers for devices that are connected to a system via a low level interface. 
These include drivers for devices such as:
 - External ADCs
 - External DACs
 - Wireless interfaces
 - Battery charger
 - Current measurement ICs
 - Etc

These drivers will be written against the low level driver API enabling easy portability between different MCU architectures.

### Debug and Introspection
ECLC provides functionality for debugging and examining a system during 
every phase of the development cycle.

A robust structured logging system makes determining the health of a system and finding the cause of a fault easier.

Trace functionality extends logging with high performance, low latency recording of time sensitive events to give visibility into time critical aspects of a system, such as exact arrival of network packets, commutation signals for motor control, RTOS task switching and more.

For the sadly inevitable faults, an error handling library captures relevant system state and makes it available for debugging in realtime, or in the future with integrations with the telemetry and logging system. 

The fault handling library also integrates with the firmware update library to enabling robust fallback onto known good firmware state in the case of certain faults.

### Rich Introspection 
The rich introspection library allows firmware to make meaningful data about a running system available in a tree data structure. This allows host side tools to quickly drill down into the value of a register, a raw ADC reading, or a scaled and filtered temperature reading. Integration with the logging and trace system allows the developer to record trace information from any value available in the tree.

To enable high performance tracing of certain values when hardware support is available, ECLC provides an API and structure for integrating specialized trace hardware into the tree data structure.

### RPC and Machine Consumable API

Most firmware does not live in a vacuum. ECLC provides libraries for exposing firmware functionality remotely using a Remote Procedure Call (RPC) system. The RPC system can run on top of a variety of physical and logical links, such as UART, Ethernet, CAN, or tunnelled within higher level protocols such as CANOpen. 

Robust libraries are available for a number of these communication methods, and examples are provided for supporting new communication channels.

### Telemetry
The ECLC telemetry library provides a variety of methods to store and transmit information about the state of a system. Any data logged or traced can be sent over telemetry, with configurable policies for scheduling transmission, handling overflow and more. 

### Firmware Update

Firmware update functionality is provided in a pluggable manner.
    
 - New image upload
 - Pluggable security modules
 - Flash write mechanisms

Complete implementations are provided for a number of platforms.

### Configuration and Calibration
A configuration and calibration system including host side utilities is provided.

The configuration system supports a number of workflows and storage mechanisms:
1) Burnt into the firmware image
2) Appended to the firmware image as a binary blob
3) Loaded at runtime from a separate block storage devices

The configuration system supports default values, SI units, custom configuration blobs, versioning, fallback, and runtime reconfiguration.

### Filesystem
ECLC provides a file system abstraction supporting a variety of open and proprietary backends, all built on top of the ECLC block storage abstraction.

### Database

ECLC provides ready to use ports of several databases, including Sqlite 3, and TDB.

## Testing, Verification and Validation

To be usable in a professional and mission critical environment, ECLC will be rigorously tested in several ways.

 - Software only Unit testing with Code Coverage
 - Unit testing with simulated hardware
 - Unit testing on representative hardware
 - Static Analysis
 - Fuzzing
 - Code Review
 - End-to-end testing on a variety of real hardware platforms
 - End-to-end testing with fault injection

## Warranty and Liability

Although efforts have been made to make this code as robust and reliable as possible, it is provided as is no
other warranty, express or implied. We hereby disclaims all implied warranties, including any warranty of
merchantability and warranty of fitness for a particular purpose.

Use at your own risk.

If you or your organization requires a guarantee or warranty, contract or other arrangement, please reach out to us.

## Directory layout

The directory layout is a work in progress.
Goals:
 - Minimal coupling. It should be possible to use a subset of the libraries without requiring pulling in extraneous code
 - Easy configurability to support various platforms

This project aims to support a variety of CPU architectures, boards, RTOSs. 

There is a spectrum of configurability from hard-codec compile time configuration to one "super binary" that supports all architectures and permutations. ECLC aims to strike a balance, with simplicity being a key design goal.

### Knobs
 - Platform (roughly a single compilation target)
 - Specific CPU/Chip (STM32f407 vs STM32F405) (different set of compatible peripherals)
 - Specific board revision (Different pinouts)
 - Different middleware

#### Dynamic Configuration
It would be pretty "cool" if the same binary could be used on a number of different targets.

For this to work, at least a few constraints would need to be satisfied

Constraints: 
 - CPU Architecture
 - Memory map (or maybe fPIC if code is loaded with a bootloader)

In theory, a driver model could load different drivers depending on what is present on the device, effectively building up an API surface for the application layer.

If the whole system uses dependency injection of some soft this could work without making serious tradeoffs in the design.

```C++
    // Since the filesystem only requires a single block_device reference, as long as that object is created before the 
    // constructor, it doesn't really matter how it happens. It could be statically in a main function, 
    // or dynamically with some complex runtime dependency resolution.
    Filesystem filesystem(block_device)
```


    
