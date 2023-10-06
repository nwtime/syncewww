---
title: "Documentation"
description: "Documentation for Renesas synced."
---

#### Table of Contents

* [Hardware](/synced/documentation/#hardware)
* [Software](/synced/documentation/#software)
* [Configuration](/synced/documentation/#configuration)
* [Management API](/synced/documentation/#management-api)
* [Event Callback Functions](/synced/documentation/#event-callback-functions)
* [Acronyms](/synced/documentation/#acronyms)

***

#### Hardware

<figure class="figure">
  <img src="/synced/figure1.jpg" class="figure-img img-fluid">
  <figcaption class="figure-caption">
    Figure 1. Hardware inter-connection between SyncE ports, external clocks, inputs, outputs, the DPLL device, and the CPU running <code>synced</code> application.
  </figcaption>
</figure>

One or more external multiplexers can be used to connect multiple SyncE ports to a clock.

The multiplexers can be both single-output or dual-output:

<figure class="figure">
  <img src="/synced/mux.jpg" class="figure-img img-fluid">
  </figcaption>
</figure>

Those ports connected to external multiplexers, which are not associated to a clock, are called SyncE monitoring ports. The transition of a SyncE port from monitoring state to active state can be done automatically using the event callback functions and the management APIs, or can be initiated by the user.

<figure class="figure">
  <img src="/synced/figure2.jpg" class="figure-img img-fluid">
  <figcaption class="figure-caption">
    Figure 2. Software block diagram and the connection with the DPLL device..
  </figcaption>
</figure>

***

#### Software

`synced` application runs on CPU and interacts with the DPLL device as follows:

* Read the DPLL status (Locked, Holdover, etc.).
* Read the selected clock index to determine the current QL.
* Read the device reference monitors status to notify the user when the physical clocks qualification fails.
* Set the selected clock, or write a clock priority table into the DPLL device.

_Device APIs_ module implements the hardware abstraction layer.

The architecture is modular, and each software module performs simple tasks:
* The _ESMC stack_ notifies the _Selector_ when a SyncE port Quality Level has changed.
* The _Selector_ sets the clock priority table into the DPLL device.
* SyncE DPLL monitor reads the DPLL and clock status and sets the current QL into the ESMC stack.
* _PCM interface_ enables the communication with an external PTP Clock Manager application if needed. The purpose of this interface is to provide the current clock category.
* The management interface enables the communication with an external CLI client `SYNCED_CLI` which is included in the open-source code. `SYNCED_CLI` implements all management APIs.

***

#### Configuration

The configuration file has two sections:

**Global**:
* ESMC network option
* No QL mode enable
* SyncE port forced QL enable
* LO QL
* LO Priority
* Device config file name
* SyncE DPLL index
* Holdover QL
* Holdover timer in seconds
* Hold-off timer in milliseconds
* Wait-to-restore timer in seconds
* PCM interface enable, IP address and port number
* Management interface enable, IP address and port number
* Syslog enable (configurable message level)
* Stdout enable (configurable message level)

**Per port**:
* [name] (ex: [eth0])
* Clock index
* Priority
* TX enable
* RX enable
* TX bundle number
* Initial QL

| Port type | [name] | Clock index | Priority | TX enable | RX enable | TX bundle number | Initial QL |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Active SyncE clock | m | m | m | opt | 1 | opt | opt/n |
| External clock | m | m | m | n | n | N | opt/n | 
| SyncE monitoring | m | n | m | opt | 1 | opt | opt/n |
| TX only | m | n | n | m | n | opt | n |

**m** = mandatory, **n** = must not be defined, **opt** = optional, default value applies if missing, **1** = must be set to 1

***

#### Management API

`synced` provides a set of management APIs to get information and set parameters. These APIs can be either called directly if the source code is integrated in other projects, or they can be called by other applications via _Management Interface_. This interface can be enabled and configured in the configuration file.

The APIs return one of the following response codes:

| Response code | Response | Details |
| :---: | --- | --- |
| 0 | Ok | The command finished successfully. |
| 1 | Invalid | One or more input arguments are invalid. |
| 2 | Failed | Command failed. Ex: port name cannot be found. |
| 3 | Not Supported | The command is not supported in current operation mode. |

1. Get a list of sync information structures, one for each port:
   * Port name
   * Port sync type (Active SyncE, External, Monitoring or TX only)
   * Type-specific information:

   | Information | Active SyncE | External | Monitoring | TX only |
   | --- | :---: | :---: | :---: | :---: |
   | priority | x | x | x | |
   | current QL | x | x | x | |
   | sync state | x | x | x | |
   | TX bundle number | x | | x | x |
   | clock index | x | x | | |
   | hoff/wtr remaining time [ms] | x | | x | |
   | number of hops | x | | x | |
   | rank | x | x | x | |
   | sync clock state | x | x | | |
   | clock reference monitor status | x | x | | |
   | RX timeout flag | x | | x | |
   | port link-down flag | x | | x | x |

2. Get a sync information structure for a specific port. The application provides the information for the specified port name.
3. Get current status:
   * Current QL
   * Selected port name
   * Selected clock index
   * SyncE DPLL state
   * Holdover remaining time in milliseconds
4. Set forced QL per port
5. Clear forced QL per port
6. Clear holdover timer
7. Clear SyncE clock wait-to-restore timer per port
8. Assign a new SyncE port to a clock index
9. Set priority per port

***

#### Event Callback Functions

The purpose of the callback functions is to notify the user when an event occurs. The implementation is on user side; however, `synced` contains template code for each callback and examples on how to control the external multiplexers.

1. Current QL
2. Sync port current QL
3. SyncE DPLL current state (Locked, Holdover, etc)
4. Sync port current clock state (Qualified, Unqualified)
5. Sync port current state (Normal, Forced, Wait-to-restore, Hold-off)
6. Alarms:
   * Timing loop detected
   * Invalid QL received at port
7. PCM interface status

***

#### Acronyms
	

| Acronym | Definition |
| --- | --- |
|DPLL | Digital Phase Lock Loop |
| DNU | Do-not-use |
| ESMC | Ethernet Synchronization Messaging Channel |
| ITU-T | International Telecommunication Union – Telecommunication Standardization Sector |
| LO | Local Oscillator |
| MUX | Multiplexer |
| PCM | PTP Clock Manager |
| PTP | Precision Time Protocol |
| PTP4L | Precision Time Protocol for Linux |
| QL | Quality Level |
| TLV | Type-Length-Value |

***
<br>

<div style="text-align: center">
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="https://github.com/renesas/synced">Source Code <img src="/images/icons/github.svg"></img></a>
    </button>	
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="https://github.com/renesas/linux-ptp-driver-package">Download RSMU driver  <img src="/images/icons/download.svg"></img></a>
</button>
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="https://github.com/renesas/synced/blob/main/README.md">README <img src="/images/icons/book.svg"></img></a>
</button>
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="/synced/documentation/">Documentation <img src="/images/icons/folder.svg"></img></a>
</button>
</div>
<br>