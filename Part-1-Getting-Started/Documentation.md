# Active Directory Lab — AD-Server & Splunk SIEM

> Windows Server (`AD-Server`) integrated with an Ubuntu Splunk SIEM for centralized security monitoring and detailed endpoint telemetry.

## 1. Executive Summary

This documentation describes the architecture and deployment of a Windows Server endpoint (`AD-Server`) integrated with a centralized Ubuntu Splunk SIEM.

The setup is designed to provide visibility into:

- Windows authentication activity
- System activity
- Process execution
- Network behavior
- Detailed endpoint telemetry through Sysmon

AD-Server telemetry is kept separate in a dedicated Splunk index, `endpoint-ad`, to make investigation and threat hunting easier.

## 2. High-Level Architecture

**AD-Server → Splunk Universal Forwarder → Ubuntu Splunk Server → `endpoint-ad` index**

AD-Server generates Windows Event Logs and Sysmon telemetry. The Splunk Universal Forwarder collects selected events and forwards them over TCP port `9997` to the Ubuntu Splunk server, where the data is indexed and made available for investigation.

### Architecture Diagram

Place `architecture-diagram.png` in this same folder:

![AD-Server telemetry architecture](architecture-diagram.png)

## 3. Architecture Components

### 3.1 Endpoint Layer — AD-Server

- Operating System: Windows Server
- Hostname: `AD-Server`
- Windows Event Logs: Application, Security, and System
- Microsoft Sysmon configured using Olaf Hartong's advanced `sysmonconfig.xml`
- Sysmon provides process lineage, command-line activity, network connections, driver loads, and other detailed endpoint telemetry.

### 3.2 Forwarding Layer — Splunk Universal Forwarder

- Installed on `AD-Server`
- Runs under the **Local System** account for access to restricted Windows event channels, especially Security logs.
- Forwards telemetry to the central Splunk server over TCP port `9997`.
- The local `inputs.conf` identifies the host as `AD-Server`.
- Selected logs are routed to the dedicated `endpoint-ad` index.

### 3.3 Central SIEM Layer — Ubuntu Splunk Server

- Receives Universal Forwarder data on TCP port `9997`.
- Stores AD-Server telemetry in the dedicated `endpoint-ad` index.
- Separating `endpoint-ad` from the standard `endpoint` index keeps AD telemetry organized and makes investigation and threat-hunting queries easier.

## 4. `inputs.conf` Configuration

```ini
[default]
host = AD-Server

[WinEventLog://Application]
disabled = 0
index = endpoint-ad

[WinEventLog://Security]
disabled = 0
index = endpoint-ad

[WinEventLog://System]
disabled = 0
index = endpoint-ad

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = endpoint-ad
```

## 5. Step-by-Step Implementation

### Phase 1 — Hostname Setup

1. Rename the Windows Server computer to `AD-Server`.
2. Restart the system to apply the hostname change.

### Phase 2 — Splunk Universal Forwarder

3. Install the Windows 64-bit Splunk Universal Forwarder.
4. Skip Deployment Server configuration.
5. Configure the Ubuntu Splunk Server as the receiving indexer using TCP port `9997`.

### Phase 3 — Dedicated Splunk Index

6. Open the Ubuntu Splunk Web Portal.
7. Navigate to **Settings → Indexes → New Index**.
8. Create the `endpoint-ad` index.

### Phase 4 — Local `inputs.conf`

9. Open:
```text
C:\Program Files\SplunkUniversalForwarder\etc\system\local\
```
10. Create or update `inputs.conf` using the configuration above.

### Phase 5 — Service Permissions

11. Open `services.msc`.
12. Locate **SplunkForwarder**.
13. Change the Log On account to **Local System**.
14. Restart the **SplunkForwarder** service.

### Phase 6 — Sysmon Deployment

15. Download Microsoft Sysinternals Sysmon and Olaf Hartong's `sysmonconfig.xml`.
16. Place both files in the same working directory.
17. Open PowerShell as Administrator and run:
```powershell
.\sysmon64.exe -i sysmonconfig.xml
```
18. Accept the EULA to complete the installation.

## 6. Verification & Testing

Verify that AD-Server telemetry is reaching Splunk with:

```spl
index=endpoint-ad host="AD-Server"
```

A successful search confirms that the forwarding pipeline is working and that AD-Server logs are being stored under the dedicated `endpoint-ad` index.

## 7. Expected Telemetry

- Security events for authentication and account activity
- Application and System events for operating-system and application behavior
- Sysmon events for process creation, command lines, network activity, and other endpoint behaviors
- Centralized and searchable telemetry inside Splunk for investigation and threat hunting

## 8. Lab Outcome

The `AD-Server` is configured as a monitored Windows endpoint with Sysmon generating detailed telemetry and Splunk Universal Forwarder streaming selected logs to the Ubuntu Splunk SIEM.

The dedicated `endpoint-ad` index provides a clean foundation for future Active Directory monitoring, detection engineering, and SOC investigations.

---

**Part 1 — Completed**

More parts of the Active Directory + Splunk SOC Lab will be added to this repository as the project progresses.
