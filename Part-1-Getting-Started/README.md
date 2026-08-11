# Active Directory SOC Lab — Part 1

## Overview

This is **Part 1** of my Active Directory SOC Lab project, where I started building a small enterprise-style security environment from scratch.

The goal of this project is to understand how **Active Directory, Windows endpoints, Splunk, Sysmon, and an attacker machine** can work together to create a realistic SOC lab.

## Lab Architecture

The environment consists of:

* **Active Directory Server** — Centralized identity and domain management
* **Splunk Server** — Centralized log collection, indexing, and analysis
* **Windows Target Machine** — Simulated enterprise endpoint
* **Kali Linux** — Attacker machine

The lab is designed so that security activity can be generated on Windows systems, telemetry can be collected, forwarded to Splunk, and later investigated from a SOC analyst's perspective.

## Part 1 Focus

In this part, I focused on:

* Designing the lab architecture
* Understanding how the components connect
* Planning the network and data flow
* Defining the role of each machine
* Preparing the environment for the upcoming configuration and detection work

## Documentation

📄 **Complete Part 1 documentation:**
`Part-1-Documentation.pdf`

🖼️ **Lab Architecture:**
The architecture diagram below shows the overall environment and how the components are connected.

## What's Next?

In the upcoming parts, I will continue configuring the environment, including **Active Directory, Splunk, Sysmon, log forwarding, and security monitoring**, and gradually turn the lab into a functional SOC environment.
