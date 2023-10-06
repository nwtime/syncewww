---
title: "Renesas synced"
description: "Renesas synced"
---

#### Renesas `synced`

`synced` is an open-source solution for Synchronous Ethernet (SyncE) based on the [ITU-T G.8264 (03/2018)](https://www.itu.int/rec/T-REC-G.8264-201803-I!Amd1/en) and [ITU-T G.781 (04/2020)](https://www.itu.int/rec/T-REC-G.781-202004-I/en) standards.

The main purpose of `synced` is to select and propagate the best clock based on Quality Level (QL) through the network.

Features:
* Supports multiple SyncE clocks and external clocks
* User-friendly APIs and event callback functions for easy integration
* Supports external multiplexers to combine SyncE ports in a more flexible way
* Timing loop detection and alarm
* Supports TX SyncE port bundles to avoid timing loops in the network 
* Management API and PTP Clock Manager interfaces to interconnect with other applications
* Supports all ESMC network options with extended TLVs
* The configuration file format is similar to [`ptp4l`](https://linuxptp.nwtime.org/documentation/ptp4l/) application
* Automatic detection of port link up/down

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