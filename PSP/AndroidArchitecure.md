# Android architecture
## Android platform
- mobile operating system + libraries + application + frameworks + key apps
- based on Linux
- open source
- runs on range of devices

### Key features
- process management specifically tailored for:
    - **battery powered devices** (when an app is not used it gets suspended my Android)
    - **low-memory devices** (when memory is low, suspended apps are terminated)
- support for direct manipulation interfaces (gestures, sensors, notifications)
- open ecosystem for applications (support for developing)

## Architecture
### Linux Kernel Services
#### Security management
- only kernel and a few core applications run as a root
- each app is assigned a unique UID
- each app runs in its own sandbox:
    - private memory
    - apps cannot access each other's data
- android ensures that the memory is used in a fair manner
- each app is given a set of permissions (asks user)
- bad programming is the biggest threat:
    - exposing data to other apps
    - insecure networking
    - buggy native code
    - dynamic code loading

### Power management
- screen dimming, process killing
- wakelocks -> prevent device to go to sleep
    - negative on battery life
- project Volta -> OS takes care of scheduling periodic jobs for the apps
- file and network I/O
- device drivers

## Libraries

## Runtime
### Android core libraries
- besides standard Java libraries Android includes specific libraries for mobile development

### Process virtual machine

### Compilation and workflow
- app written in Java or Kotlin
- compiled to Java bytecode files
- DX converts Java bytecode files to a single DEX bytecode file optimized for space
- .apk file is generate with the dex file and all the application resources, manifest, etc.

code -> kotlinc -> proguard (optional) -> DX -> Zip -> dex2oat

- ART:
    - when apk is installed, ART uses ahead-of-time compilation to convert it and save it as native machine code
    - every other time the app runs from the native code
- Dalvik:
    - trace-based just-in-time compilation: continuously profile apps each time they run and dynamically compile frequently executed short segments of the bytecode into native machine code

## Framework
### Package Manager
- keeps track of the installed applications

### Window Manager
- ensures that setContentView connects the given View with activity's window and is full screen
- example: floating icon over every app (Facebook)

### Content providers
- manage access to data that is exposed for inter-application sharing
- example: using existing CP to get contacts for your chat app

### Location manager
- Google play location services (optimized for energy efficiency)

### Notification manager
- means for the app to initiate interaction with user

### Resource manager
- support different:
    - screen sizes
    - languages
    - platform versions
- files are programmatically accessible via **R** file

### Activity manager
- manages application lifecycle and navigation through the stack of application pages that the user sees
- mostly used for debugging purposes or app running configuration adaptation

### View System
- for UI (represented as a hierarchy of Views)
- such a structure is called a **layout** and is defined by an XML file