# The Taiko DIY Framework: Recommendations for Interoperability

> 杏姐电控盒，不止转鼓皮
> 
> *Beyond mechanical.*

Authored by Zheng "Apricot" Lyu, designer of TaikoCatz® 杏姐® I/O dongle.

> [!Important]
> Copyright © 2026 Zheng Lyu, All rights reserved.
> 
> You may freely share this document providing that it remains unaltered and complete, including this notice. This document is provided "as is" without warranty of any kind.

## Abstract


Driven by the rapid expansion of the Taiko no Tatsujin enthusiast community in China, this document introduces the Taiko DIY Framework—a set of technical recommendations designed to resolve hardware fragmentation. It proposes a modular topology separating the physical Drum Assembly from the I/O Dongle, establishing strict standards for electrical pinouts, sensor interfacing, and USB HID protocols. By defining a unified specification for signal routing and low-latency connectivity, this framework aims to eliminate proprietary barriers, ensuring plug-and-play interoperability and fostering a collaborative, sustainable ecosystem for custom controller design.

- [The Taiko DIY Framework: Recommendations for Interoperability](#the-taiko-diy-framework-recommendations-for-interoperability)
  - [Abstract](#abstract)
  - [Background](#background)
  - [Topology and Form Factor](#topology-and-form-factor)
    - [The Drum Assembly](#the-drum-assembly)
    - [The I/O Dongle](#the-io-dongle)
  - [Sensor Interface](#sensor-interface)
    - [Selection and Design](#selection-and-design)
    - [Electrical Specifications](#electrical-specifications)
    - [Connector](#connector)
      - [Cabling Interface (Drum)](#cabling-interface-drum)
      - [Controller Interface (Dongle)](#controller-interface-dongle)
  - [System Connectivity and Interoperability](#system-connectivity-and-interoperability)
    - [Physical and Electrical Interface](#physical-and-electrical-interface)
      - [Power Requirements](#power-requirements)
      - [Connector Standards](#connector-standards)
      - [Implementation Notes](#implementation-notes)
    - [USB HID Keyboard Implementation](#usb-hid-keyboard-implementation)
    - [Software Interoperability](#software-interoperability)
      - [Keyboard Mapping](#keyboard-mapping)
    - [Unconventional Connectivity Alternatives](#unconventional-connectivity-alternatives)
  - [Future Work](#future-work)
    - [Standardization of signal levels](#standardization-of-signal-levels)
  - [Appendix: Technical Case Study: Official Taiko no Tatsujin® Arcade](#appendix-technical-case-study-official-taiko-no-tatsujin-arcade)


## Background

Following the official expansion of **Taiko no Tatsujin(R)** into mainland China by **Wahlap Technology**, the enthusiast community has seen unprecedented growth. This surge has inspired a new wave of hobbyists to design custom game controllers, ranging from personal passion projects to mini-batch commercial productions.

Despite this creative momentum, the community lacks a **unified technical framework**. Currently, hardware fragmentation is a significant barrier; components designed by one developer often utilize proprietary wiring, incompatible assumptions, or exhibit unpredictable behavior interacting with gaming platforms. This lack of standardization limits the ability for creators to exchange parts, upgrade systems, or collaborate effectively.

This document aims to address these challenges by providing a set of standardized recommendations for:

- **Hardware Topology:** Establishing a common architectural foundation to ensure that controllers, regardless of their internal design, maintain a consistent logical structure.
- **Electrical Interfacing:** Defining standardized signal protocols and connector pinouts to facilitate modularity and part-swapping between different hardware ecosystems.
- **Software Interoperability:** Identifying and mitigating common pitfalls in how hardware communicates with gaming platform to ensure high-performance, "plug-and-play" compatibility.

By adopting these guidelines, the DIY community can move toward a more modular and sustainable ecosystem - one that fosters innovation while ensuring seamless cross-compatibility across diverse hardware implementations and game platforms.


## Topology and Form Factor
A standardized Taiko controller implementation consists of two distinct functional units:
- The **Drum Assembly**
- The **I/O Dongle**

### The Drum Assembly
The drum assembly is the physical interface housing the sensors. It captures the mechanical force of a strike and converts it into an electrical format. A standard assembly utilizes **four (4) sensing points** to represent the *Don* (center) and *Ka* (or *Katsu*, rim) inputs for both left and right halves as required by the game.

### The I/O Dongle
The I/O dongle is the intelligent controller that sits between the drum assembly and the gaming platform. Its role is defined by three primary functions:

- **Sensor Interface:** The dongle provides the necessary electrical connection to receive data from the drum assembly.
- **Strike Recognition:** This internal process interprets sensor readings to identify logical *Don* and *Ka* strikes.
- **System Connectivity:** The dongle sends identified strike events to game software on the host system (PC or Console).

## Sensor Interface
The drum utilizes piezoelectric ceramic sensors to convert physical impact into electrical signals. For optimal performance and modularity, the following standards are recommended:

### Selection and Design
- **Consistency:** Use identical part numbers for all four sensors to minimize sensitivity variance.
- **Housing:** Plastic-housed sensors are preferred over bare elements.
- **Mounting:** Secure sensors using removable methods such as nano-tape or screws. Avoid permanent adhesives to ensure serviceability.

### Electrical Specifications
Adherence to these specifications is critical for interoperability. Deviations may cause suboptimal operation or hardware damage.


- **Electrical Responsibility:** The drum assembly acts strictly as a passive sensor array. A standard assembly must provide **eight (8) discrete leads** (two per sensor).
    - **Role Definition:** It is the exclusive role of the dongle to determine the grounding or connection scheme.
    - **Prohibition:** Bridging or grounding connections within the drum assembly or cabling is strictly prohibited, as this may conflict with the dongle’s internal electrical design.
    - **Isolation:** No terminal should be connected to the chassis, common ground, or any large conductive surfaces. Sensors must not share any common wiring (e.g., connecting all "black wires" together is forbidden).

### Connector
The connector scheme is a standardization and refinement of the *de facto* standard established by the pioneering design from ZhangBigPower (张大力). It utilizes an **8-conductor IDC ribbon cable** to connect the drum assembly to the I/O dongle.

#### Cabling Interface (Drum)
The drum assembly cabling must terminate in a **2.54mm pitch, 2x4 (8-position) IDC female connector**.

Each sensor pair occupies a single vertical column. The diagram below represents the pinout **view from the mating face (outside)** of the female IDC connector.
```
      (Polarized key, optional)
              ┌──────┐
      ________│      │________
     /  LD    RK    RD    LK  \
    |  ┌──┐  ┌──┐  ┌──┐  ┌──┐  |  Upper row
    |  └──┘  └──┘  └──┘  └──┘  |
    |  ┌──┐  ┌──┐  ┌──┐  ┌──┐  |  Lower row
    |  └──┘  └──┘  └──┘  └──┘  |
     \________________________/
Legend:
LK = Left Ka (Rim) LD = Left Don (Center)
RK = Right Ka (Rim) RD = Right Don (Center)
```
> [!Caution]
> **Danger!** In accordance with **Electrical Responsibility** requirements, **bridging or joining leads from different sensor columns within the cable is strictly prohibited.** Because different dongle designs may implement internal common paths in varying ways, any pre-existing connections within the cable may create an electrical conflict with the dongle's internal architecture, leading to malfunctioning or even **hardware damage**.

> [!Tip]
> **Recommendation**: Piezoelectric sensors are polarized components. It is recommended to maintain consistent polarity across all four sensors. For example, connect all sensor outer electrodes (rims) to the *Upper Row* and all inner electrodes (circles) to the *Lower Row*, or vice versa.
#### Controller Interface (Dongle)
The dongle must feature a **2.54mm pitch, 2x4 (8-pin) shrouded box header** with a polarized notch.

The diagram below represents the pinout **view from the mating face** (looking directly into the shroud) of the box header.
```
     _____________ (notch)  _____________
    |             ----------             |
    |        LK    RD    RK    LD        |
    |        ##    ##    ##    ##        |  Upper row
    |                                    |
    |        ##    ##    ##    ##        |  Lower row
    |                                    |
     ------------------------------------
Legend:
LK = Left Ka (Rim) LD = Left Don (Center)
RK = Right Ka (Rim) RD = Right Don (Center)
```

## System Connectivity and Interoperability

The I/O dongle serves as the primary communication link between the drum assembly and the host gaming platform. To ensure high performance and universal "plug-and-play" operation, the following standards are recommended.

### Physical and Electrical Interface

#### Power Requirements
The system is designed to be bus-powered via the host’s USB interface.
- **Voltage:** Standard 5V nominal.
- **Current Consumption:** It is recommended not to consume over **100mA** during normal operation to ensure compatibility with all host hubs and consoles.

#### Connector Standards
- **Recommended:** A **USB-C®** compatible receptacle is the preferred standard for modern designs.
- **Acceptable:** Type-B, Micro-B, or Mini-B receptacles are valid alternatives.
- **Discouraged:** The use of **Type-A** connectors on the dongle side is discouraged to prevent user confusion and non-standard cabling configurations.

#### Implementation Notes
- **USB-C CC Configuration:** To ensure compatibility with C-to-C cables (and proper power negotiation), both `CC1` and `CC2` pins on the receptacle must be independently pulled to **GND** via a **5.1kΩ resistor** each.
- **Development Boards:** Many DIY designs utilize Arduino-compatible boards. While these generally work well, builders should take care that some budget boards with USB-C ports omit the required CC pull-down resistors to cut costs.

### USB HID Keyboard Implementation
While Gamepad interfaces are mandatory for certain console platforms, this framework prioritizes the **HID Keyboard** standard due to its ubiquitous support and driverless operation across PC-based Taiko simulation software.

For optimal latency and timing precision, the interface should adhere to the **USB 2.0 Full Speed** standard (12Mbps) with a configured polling rate of **1000Hz** (1ms interval).

- ✅ **Recommended: Full Speed (12Mbps) @ 1000Hz.** (500Hz is acceptable).
- ❌ **Discouraged: Polling Rates < 250Hz.** Intervals of 4ms or greater introduce perceptible timing jitter (variance).
- ❌ **Discouraged: Low Speed (1.5Mbps).** This mode is strictly discouraged because polling is limited to 10ms intervals (100Hz).
- ❌ **Discouraged: High Speed (480Mbps).** While capable of 8kHz polling, High Speed is generally discouraged for this application. It adds significant hardware complexity without a meaningful benefit for this application.

> [!TIP]
> **Arduino Tip:** ATmega32U4-based Arduino boards (such as the Pro Micro or Leonardo) using the standard `Keyboard` library generally meet these requirements out-of-the-box.

> [!NOTE]
> **Why 1000Hz?**
> 
> The jump from 250Hz (4ms) to 1000Hz (1ms) provides a perceptible reduction in input variance (jitter), which is critical for advanced players targeting "All Perfect" judgments. However, moving beyond 1000Hz to 8kHz offers diminishing returns: the latency reduction is imperceptible and statistically negligible. This topic is worth another deep-dive article.

### Software Interoperability


To ensure immediate compatibility with the widest range of PC-based Taiko-like games, it is recommended to adhere to standard input mapping.


#### Keyboard Mapping
> [!TIP]
> Notation: `DF-JK` refers to using `D`, `F`, `J`, `K` keys to represent *left Ka*, *left Don*, *right Don*, and *right Ka* respectively.

* **Default Mapping (`DF-JK`):** The dongle should implement the `DF-JK` layout by default. This is the de-facto standard for most games requiring no configuration.
* **Multi-Player Support:** To support 2-player modes on a single PC, user-selectable mappings are recommended. Common standards include `RT-IO`, `ZX-CV`, or `CV-NM`.


### Unconventional Connectivity Alternatives
For experimental purposes or hardware lacking native USB support (e.g., standard Arduino Uno/Nano), developers may utilize **UART Serial Bridging**.

* **Hardware Topology:** The controller transmits data via a standard UART (Serial) interface to a USB-to-TTL adapter connected to the host.
* **Host Middleware:** Unlike native HID, this topology requires a dedicated **software bridge** running on the PC to read the incoming serial stream and inject virtual keystrokes into the operating system.

## Future Work

### Standardization of signal levels
Currently, this framework does not define specific electrical signal levels (voltage ranges) due to the wide variety of piezoelectric sensors, drum structure designs, and materials in use. The expectations of signal levels are often determined by test-and-tune processes unique to each hardware implementation. Moreover, the striking strength and style of individual players can vary widely. With these factors in mind, defining standards for signal levels is challenging and maybe eventually impractical.



## Appendix: Technical Case Study: Official Taiko no Tatsujin® Arcade


To understand the necessity of the Taiko DIY Framework, we analyzed the topology of the Official Taiko no Tatsujin® Arcade hardware by play-testing and technical inference.

While iconic, the arcade architecture suffers from legacy constraints:

- The topology is completely different from the DIY Framework:
  - The arcade system forwards "strength" values (numbers) all the way to the game software. The game software then interprets the data to determine strike events. This can be inferred from the service mode of the arcade showing hit strength values.
  - By comparing photos from the internet of disassembled boards, roughly the same design is used across multiple arcade generations, from AC7 to AC16.
- Drum
  - Build quality is generally good, however:
  - Some batches have severe tolerance issues.
  - Heavy-duty arcades for public use is prone to severe damage and wear caused by inexperienced players striking very hard.
- Overall strike recognition
  - Very poor in reliability, mediocre in performance.
  - Many players report frequent missed notes and phantom hits.

The official arcade hardware suffers from severe limitations in reliability and performance, primarily due to its legacy design. This analysis underscores the need for a standardized framework to improve the quality and interoperability of custom Taiko controllers.

