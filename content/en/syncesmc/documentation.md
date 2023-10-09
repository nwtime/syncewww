---
title: "Documentation"
description: "Documentation for SyncMonk SyncESMC"
---

# Documentation

#### Table of Contents

* [Overview](/syncesmc/documentation/#overview)
   * [Synchronization Network Topology](/syncesmc/documentation/#synchronization-network-topology)
   * [Why SyncESMC](/syncesmc/documentation/#why-syncesmc)
* [Architecture](/syncesmc/documentation/#architecture)
   * [Design Goals](/syncesmc/documentation/#design-goals)
   * [Configuration](/syncesmc/documentation/#configuration)
   * [Packet Engine](/syncesmc/documentation/#packet-engine)
   * [ESMC State Machine](/syncesmc/documentation/#esmc-state-machine)
   * [External MUX for Input Signals](/syncesmc/documentation/#external-mux-for-input-signals)
   * [Hardware Abstraction Layer](/syncesmc/documentation/#hardware-abstraction-layer)
   * [External Interface](/syncesmc/documentation/#external-interface)

***

#### Overview

* ESMC is a Logical Channel which transmits SSM information.
* The SSM information determines the Quality level of EEC.
* It is based on ITU-T G.8264 (03/2018) and ITU-T G.781 (04/2020)
* Ethernet SSM is ITU-T-defined Ethernet Slow Protocol.

<figure class="figure">
  <img src="/syncesmc/figure1.png" class="figure-img img-fluid">
</figure>

***

##### Synchronization Network Topology

<figure class="figure">
  <img src="/syncesmc/figure2.png" class="figure-img img-fluid">
</figure>

***

##### Why SyncESMC

* Hardware Abstracted ESMC stack.
* Compliant to G.781(04/2020) and G.8264 (03/2018).
* Simplified Single Threaded architecture.
* Interaction with PTP Stack (to support IWF).
* Dynamic configuration support.
* Monitoring with FishEye.
* Supports Intel<sup>&reg;</sup> E810XXVDA4T, Renesas<sup>&reg;</sup> ClockMatrix<sup>&trade;</sup> 8A34001, and AuraSemi<sup>&reg;</sup> 5508 PHC.

***

#### Architecture

<figure class="figure">
  <img src="/syncesmc/figure3.jpg" class="figure-img img-fluid">
</figure>

***

##### Design Goals

* Single Threaded architecture.
* Multiple hardware support using HAL.
* Multi Clock Support. 
* External MUX for input signals.
* Futuristic design to support kernel driver support with minimal changes.
* Interaction with multi-vendor PTP stacks.
* In-built support for network configuration and monitoring
* Micro Service compatible for [SONiC](https://en.wikipedia.org/wiki/SONiC_(operating_system)).

***

##### Configuration

Configuration Module has two modes:

1. Static Configuration Mode
   * JSON-based configuration file.
   * System boots up with the predefined configuration 
   * Configuration is majorly classified into
     * Device 
     * Port
     * Monitoring
2. Dynamic Configuration
   * REST API
   * Netconf-yang model for O-RAN

***

<div class="container-fluid">
  <div class="row">
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <h5>Packet Engine</h5>
      <p>Packet Engine is involved with the interface-related activity.
	  <ul>
	    <li>Packet RX</li>
		<li>Packet TX</li>
		<li>Port Link up/ Link down Events (Network Link)</li>
		<li>Packet filtering</li>
	  </ul>
	  </p>
	  <p>Different types of Sockets and interface types are separated using packet engine library.
	  <ul>
	    <li>Raw Socket</li>
		<li>UDS Socket</li>
		<li>APIs</li>
	  </ul>
	  </p>
	</div>
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <figure class="figure">
        <img src="/syncesmc/figure4.png" class="figure-img img-fluid">
      </figure>
    </div>
  </div>
</div>

***

<div class="container-fluid">
  <div class="row">
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <h5>ESMC State Machine</h5>
      <ul>
	    <li>Core of the ESMC stack.</li>
		<li>Interacts with the Packet Engine, Configuration Engine, HAL.</li>
		<li>Decides to select the source port based on the Best Port Selection Algorithm (Quality, Priority, Hops).</li>
		<li>Handles the timer interrupts for Time outs for Hold off time,  no activity on the port, Holdover timeout and Restore timers.</li>
		<li>Interacts with HAL to configure the Hardware.</li>
	  </ul>
	</div>
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <figure class="figure">
        <img src="/syncesmc/figure5.png" class="figure-img img-fluid">
	  <ol class="figure-caption">
	    <li>Packet Engine updates the packet metadata to Database.</li>
		<li>Configuration can be updated at run time.</li>
		<li>Timer-based events for TX packets, Holdover events, and RX timeout events.</li>
		<li>Hardware events like LOS, OOF, PLL Loss.</li>
		<li>PLL ID and PLL input selection instructions to HAL.</li>
	  </ol>
      </figure>
    </div>
  </div>
</div>

***

<div class="container-fluid">
  <div class="row">
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <h5>External MUX for Input Signals</h5>
      <ul>
	    <li>Supports Multiplexing, where it can be configured for the maximum number of Sources available for a PLL and the maximum number of inputs which can be given to the PLL.</li>
		<li>Runs an algorithm to select the best ports and based on priority programs the MUX.</li>
		<li>Works with any of the available hardware where the number of inputs to PLL is more than the number of sources available. For e.g Broadcom XGS chips for Data Center switches.</li>
	  </ul>
	</div>
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <figure class="figure">
        <img src="/syncesmc/figure6.png" class="figure-img img-fluid">
      </figure>
    </div>
  </div>
</div>

***

<div class="container-fluid">
  <div class="row">
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <h5>Hardware Abstraction Layer</h5>
      <ul>
	    <li>Hardware vendors can plugin their HAL functions to integrate with SyncESMC.</li>
		<li>Ready for Kernel exposed APIs (in future) for controlling the hardware.</li>
		<li>Hardware interrupt and status monitoring.</li>
		<li>Multiple vendors’ PLLs can be controlled by the same stack.</li>
	  </ul>
	</div>
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <figure class="figure">
        <img src="/syncesmc/figure7.png" class="figure-img img-fluid">
      </figure>
    </div>
  </div>
</div>

***

<div class="container-fluid">
  <div class="row">
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <h5>External Interface</h5>
      <ul>
	    <li>UDS socket can be used to interact with external applications.</li>
		   <ul>
		    <li>PTP stack</li>
		    <li>Seamless integration with any PTP stack over a UDS socket.</li>
		    <li>SyncESMC can convert Clock Class to Clock quality as per G.8275.2  Annex F.</li>
		  </ul>
		<li>External Application Interfaces</li>
		  <ul>
		    <li>FishEye</li>
		    <li>Configurations</li>
		  </ul>
	  </ul>
	</div>
    <div class="col-xl-6 col-lg-6 col-md-6">
	  <figure class="figure">
        <img src="/syncesmc/figure8.png" class="figure-img img-fluid">
      </figure>
    </div>
  </div>
</div>




***

<br>

<div style="text-align: center">
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="">Coming Soon <img src="/images/icons/github.svg"></img></a>
    </button>
<button class="btn btn-primary btn-lg">
  <a class="btn btn-primary btn-lg" href="/syncesmc/documentation/">Documentation <img src="/images/icons/folder.svg"></img></a>
</button>
</div>
<br>