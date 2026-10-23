# INC-002 — Windows Network Discovery Failure (Error 6118)

**Category:** Networking / Windows Troubleshooting  
**Environment:** Windows 11 Pro VM (UTM on macOS)  
**Network Configuration:** Shared Network (NAT)  
**Status:** Investigation Completed — Root Cause Unconfirmed  
**Priority:** Low (No Internet Connectivity Impact)

---

## 1. Issue Description

While practising Windows Command Prompt networking commands in my Windows 11 Pro virtual machine, I attempted to list available network computers and servers using:

```cmd
net view
```

The following error occurred:

```text
System error 6118 has occurred.

The list of servers for this workgroup is not currently available.
```

The objective was to investigate why network enumeration failed despite the virtual machine having functional internet access.

## 2. Troubleshooting Process

### Step 1 — Verify IP Configuration

**Command:**

```cmd
ipconfig /all
```

**Findings:**

| Parameter | Result |
|---|---|
| IPv4 Address | 192.168.64.2 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.64.1 |
| DHCP Server | 192.168.64.1 |
| Network Adapter | Red Hat VirtIO Ethernet Adapter |
| DHCP Enabled | Yes |

**Analysis:**

The virtual machine had obtained a valid IPv4 address through DHCP. Its default gateway and DHCP server were consistent with the UTM virtual network configuration.

No obvious IPv4 addressing problem was identified.

### Step 2 — Test Internet Connectivity

**Command:**

```cmd
ping 1.1.1.1
```

**Results:**

- Packets sent: 4
- Packets received: 4
- Packet loss: 0%
- Average round-trip time: 7 ms

**Analysis:**

The virtual machine successfully communicated with an external IP address, indicating that basic internet connectivity was functional.

### Step 3 — Verify DNS Resolution

**Command:**

```cmd
nslookup google.com
```

**Results:**

```text
Server: UnKnown
Address: fe80::a09a:8eff:fe13:fa64

Non-authoritative answer:
Name: google.com
Address: 142.250.183.46
```

**Analysis:**

DNS resolution was successful.

Although the DNS server was displayed as `UnKnown`, this did not indicate a DNS failure. The server name could not be resolved, but the DNS query itself returned valid addresses.

DNS resolution was therefore unlikely to explain the network enumeration failure.

### Step 4 — Investigate Network Discovery Services

**Commands:**

```cmd
sc query FDResPub
sc query fdPHost
```

**Results:**

Both services were in a `STOPPED` state, with Windows exit code 1077.

Further investigation:

```cmd
sc qc FDResPub
sc qc fdPHost
```

Both services were configured as:

```text
START_TYPE : 3 DEMAND_START
```

**Analysis:**

The services were configured for manual, on-demand startup.

Windows exit code 1077 indicated that the services had not been started since the last system boot.

Therefore, the stopped state did not, by itself, establish a service failure.

The relationship between these services and Error 6118 remained unconfirmed.

### Step 5 — Inspect Windows Network Profile

**Command:**

```cmd
powershell -Command "Get-NetConnectionProfile"
```

**Result:**

```text
NetworkCategory: Public
```

I then inspected:

Settings → Network & Internet → Advanced Network Settings → Advanced Sharing Settings

**Findings:**

- Active Network Profile: Public
- Public Network Discovery: Off
- Public File and Printer Sharing: Off
- Private Network Discovery: On (inactive profile)

**Analysis:**

Windows was applying a Public network profile, where network discovery was disabled.

This was identified as a possible contributing factor to limited network visibility. However, Error 6118 was not conclusively linked to this setting.

No firewall or sharing settings were changed.

### Step 6 — Examine Virtual Network Configuration

The Windows 11 virtual machine was running in UTM on macOS.

The UTM network mode was confirmed as:

**Shared Network (NAT)**

**Analysis:**

The virtual machine used a virtual network to access external networks through the host environment.

This configuration can affect network discovery and broadcast-based browsing.

However, NAT does not inherently prevent a virtual machine from accessing an SMB server, and no specific SMB server had been identified for testing.

### Step 7 — Test Default Gateway Connectivity

**Command:**

```cmd
ping 192.168.64.1
```

**Results:**

- Packets sent: 4
- Packets received: 4
- Packet loss: 0%
- Reported average latency: 0 ms

**Analysis:**

The virtual machine successfully communicated with its default gateway.

This supported the earlier finding that basic local IP connectivity was functional.

### Step 8 — Test TCP Port 445

**Command:**

```cmd
powershell -Command "Test-NetConnection 192.168.64.1 -Port 445"
```

**Results:**

```text
ComputerName     : 192.168.64.1
RemotePort       : 445
SourceAddress    : 192.168.64.2
PingSucceeded    : True
TcpTestSucceeded : False
```

**Analysis:**

The gateway was reachable using ICMP, but a TCP connection to port 445 could not be established.

Port 445 is commonly used for SMB file sharing.

However, the virtual gateway was not confirmed to be running an SMB service. Therefore, this result did not establish a fault in the Windows SMB client or the virtual network.

---

## 3. Findings and Assessment

The investigation established that:

1. The virtual machine had a valid DHCP-assigned IP configuration.
2. External IP connectivity and DNS resolution were functioning.
3. The default gateway was reachable.
4. Windows was using a Public network profile with network discovery disabled.
5. The network discovery services were stopped but configured for on-demand startup.
6. The UTM virtual machine used Shared Network (NAT).
7. TCP port 445 was not accessible on the tested gateway.

**Root Cause: Not Confirmed**

The failure may relate to Windows network browsing mechanisms, discovery settings, or the absence of discoverable SMB devices in the virtual network.

Importantly, the legacy browsing behaviour of `net view` does not provide a complete assessment of modern Windows network discovery.

No evidence established a general network connectivity failure.

## 4. Resolution Status

**Status: Unresolved — Further Investigation Deferred**

The investigation was intentionally stopped after establishing that basic IP connectivity and DNS resolution were operational.

No system configuration changes were applied.

A future controlled lab could introduce a known SMB file server to test actual file-sharing connectivity and distinguish server availability from network discovery limitations.

## 5. Key Learning Outcomes

Through this investigation, I gained practical experience in:

- Using `ipconfig /all` to inspect network configuration.
- Testing connectivity using `ping`.
- Verifying DNS resolution using `nslookup`.
- Inspecting Windows services using `sc query` and `sc qc`.
- Understanding Public and Private network profiles.
- Recognising the networking implications of virtualisation and NAT.
- Testing TCP port connectivity using PowerShell.
- Distinguishing IP connectivity, DNS resolution, service availability, and network discovery.
- Avoiding premature conclusions about root causes.

**Key Takeaway:**

A failed network discovery command does not necessarily indicate a network connectivity failure. Effective troubleshooting requires testing individual network components, interpreting results in context, and distinguishing confirmed findings from hypotheses.

---

## 6. Evidence

Screenshots collected during the investigation:

- E01 — Initial `net view` Error 6118
- E02 — `ipconfig /all` network configuration
- E03 — External IP connectivity and DNS resolution tests
- E04 — Network discovery service status
- E05 — Service startup configuration
- E06 — Windows Advanced Sharing Settings (Public profile)

**Evidence Note:** Screenshots should be reviewed to ensure that usernames, MAC addresses, and other identifying information are appropriately redacted before public publication.