# Lab 01: macOS Wi-Fi Connectivity Troubleshooting

**Category:** Network Troubleshooting
**Date:** 2026-10-22
**Environment:** macOS, Shared Residential Wi-Fi
**Priority:** Medium
**Technologies:** DHCP, IPv4, TCP/IP
**Status:** Resolved
**Documentation Type:** Retrospective Case Study

## 1 Object
To document and analyse a real-world Wi-Fi connectivity issue encountered on my MacBook, with a focus on DHCP configuration, IP address conflicts, and basic network troubleshooting.

## 2 Problem Description
My MacBook experienced intermittent connectivity issues when connecting to my apartment's shared Wi-Fi network.

Other devices, including my iPhone, were able to connect to the same network, suggesting that the issue might be related to the MacBook's network configuration or its interaction with the shared network.

## 3 Troubleshooting steps
During troubleshooting, I performed the following actions:

1. Opened System Settings → Wi-Fi → Details → TCP/IP to investigate the IPv4 configuration.
2. Used the Renew DHCP Lease function.
3. Changed the IPv4 configuration to manual assignment and entered 192.168.18.200.
4. Received an IP address conflict warning indicating that the address was already being used by another device.
5. Changed the IPv4 configuration back to Using DHCP.
6. Confirmed that the MacBook's Wi-Fi connectivity was restored.

Note: The exact sequence of the DHCP lease renewal relative to the other configuration changes was not recorded.

## 4 Root cause
Root Cause: Unconfirmed

Possible explanations include:

1. An issue with the existing DHCP lease or IPv4 configuration.
2. Network settings being refreshed after switching between manual and automatic configuration.
3. An intermittent issue with the shared Wi-Fi network.

The IP address conflict warning confirmed that the manually selected address was already in use. However, this conflict occurred during troubleshooting and was not established as the cause of the original connectivity issue.

## 5 Resolution
Connectivity was restored after changing the IPv4 configuration back to automatic DHCP assignment and performing DHCP-related troubleshooting.

The specific action responsible for resolving the issue could not be conclusively identified.

## 6 Evidence and Limitations

This case was documented retrospectively after the issue had been resolved.

The following evidence was not collected during the incident:

1. Screenshots of the original connectivity failure.
2. Terminal output showing the original IP configuration.
3. DHCP lease details before and after troubleshooting.
4. Ping and DNS test results.

As a result, the root cause remains unconfirmed.
No screenshots or command outputs have been recreated or presented as evidence from the original incident.

## 7 Key Learnings

Through this experience, I developed a better understanding of:
DHCP: Automatically assigning IP addresses and network configuration.
DHCP Lease: The temporary allocation of an IP address to a client.
IP Address Conflict: The problems that can occur when two devices use the same IPv4 address on the same network.
Automatic vs Manual IP Configuration: The differences between DHCP-assigned and manually configured addresses.
Troubleshooting Documentation: The importance of recording system information before making changes.

## 8 Future Improvements

For future network troubleshooting incidents, I will:

Capture the original error message and network configuration.
Record the IP address, subnet mask, default gateway and DNS settings.
Use Terminal commands such as ifconfig, ipconfig getpacket, and route -n get default.
Perform network connectivity tests before and after troubleshooting.
Change one configuration setting at a time to isolate the cause.
Save screenshots and command outputs as supporting evidence.

## 9 Final Outcome

Result: Connectivity restored.
Root Cause: Not conclusively identified.
Skills Practised: DHCP troubleshooting, IPv4 configuration, IP conflict identification, and incident documentation.
Reflection: This incident demonstrated the importance of collecting diagnostic evidence before applying fixes. In future cases, I will follow a more structured troubleshooting process to improve the accuracy of my root cause analysis.
