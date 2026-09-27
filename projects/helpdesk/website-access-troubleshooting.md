Help Desk Lab: Website Access Troubleshooting and Tier 2 Handoff
Author: Edward Sparks
Date: September 27, 2026
Scope: Tier 1 help desk
Outcome: Investigation completed to the Tier 1 access boundary; escalation documentation prepared. Resolution and assignment to Tier 2 were not verified.
Overview
In this simulated help desk exercise, I investigated a report that a Finance user could not open websites. I practiced customer communication, checking workstation network settings, gathering diagnostic evidence, and recognizing when additional access was required. The exercise used osTicket and a Windows domain lab.
This was a training scenario, not a production incident. ChatGPT helped prepare the scenario, role-play the customer, and provide feedback on my troubleshooting and communication.
Environment
Component	Purpose
WIN10LAB	Windows 10 domain workstation
DC01	Active Directory and DNS for ad.homelab.test
Finance share	Internal resource used to compare local access with website access
Ubuntu with Docker	Hosted osTicket and Mailpit
VMware Workstation	Virtual lab environment


Before introducing the fault, I confirmed that WIN10LAB could open a website and access the Finance share using an authorized account. A manual proxy pointing to 127.0.0.1:65534 was then enabled to simulate a browser connection problem. This setup knowledge was kept separate from the evidence available to the technician during the role-play.
Reported issue and initial questions
The simulated customer, Sarah Foster in Finance, reported that websites would not load and the browser indicated it was not connected.
I asked whether she was in the office or working remotely, whether coworkers were affected, and whether she could access shared drives. In the role-play, Sarah reported that she was in the office, a coworker could browse normally, and she could still access Finance files.
These answers narrowed the reported scope to one workstation. They did not prove that all internet connectivity was unavailable.
Workstation checks
I reviewed ipconfig /all:
Setting	Observed value
IPv4 address	192.168.100.11
Subnet mask	255.255.255.0
Default gateway	192.168.100.2
DNS server	192.168.100.10


The values matched the intended lab configuration, but configuration alone did not prove connectivity. I also learned that IP Routing Enabled, DHCP Enabled, and WINS Proxy Enabled showing “No” were not automatically faults: this workstation used a static address and was not intended to route traffic or provide WINS proxy services.
I ran nslookup google.com on WIN10LAB. It identified DNS server 192.168.100.10 and returned a DNS request timeout. “Server: Unknown” alone was not sufficient to diagnose a DNS failure; the timeout was the significant finding. Its cause remained unresolved and needed to be included in the handoff.
A separate lab administration check on DC01 successfully resolved a name locally. That did not establish that WIN10LAB could receive answers from DC01. Server administration was excluded from the subsequent Tier 1 workflow.
Browser evidence and access boundary
I asked the customer for a screenshot of the browser error and offered to walk her through taking one. In the simulated response, the customer reported ERR_PROXY_CONNECTION_FAILED and a message indicating a problem with the proxy server or its address. The exact code was supplied in the role-play, not verified from an uploaded browser screenshot.
My next proposed check was to compare the user's proxy settings with the approved configuration:
Setting	Approved lab baseline
Automatically detect settings	On
Use setup script	Off
Use a proxy server	Off


The user could not access workstation settings. No approved technician access method was established for this exercise, so I prepared a handoff to Tier 2/Desktop Support instead of bypassing the restriction. A proxy configuration issue remained suspected during the technician workflow; the known fault introduced during setup did not replace diagnostic verification.
Customer communication
My escalation update was:
Sarah, I'm going to escalate your issue to a tier 2 technician who has access to investigate further.

I removed an earlier promise that she would hear back “shortly” because no response timeframe had been confirmed.
Prepared internal handoff
Issue: Sarah Foster reports that websites will not load on WIN10LAB.
Scope: Customer reports being in the office. A coworker can browse websites. Customer reports continued access to the Finance share.
Checks: Workstation IPv4 192.168.100.11/24, gateway 192.168.100.2, DNS 192.168.100.10. nslookup google.com returned a DNS request timeout. Browser error reported in the simulation: ERR_PROXY_CONNECTION_FAILED.
Assessment: Suspected proxy configuration issue based on the browser error. Proxy settings not verified during Tier 1 troubleshooting. DNS timeout remains an additional unresolved finding.
Reason for escalation: User cannot access settings, and no authorized Tier 1 method to inspect or change them was established.
Requested action: Tier 2/Desktop Support to inspect the affected user's proxy settings against the approved baseline and investigate the DNS timeout as needed. Verify website access and continued Finance share access after remediation.
Ticket handling: Keep the ticket open and assign according to the support process. This write-up does not confirm that assignment occurred or that service was restored. Reference any screenshot already attached to the ticket; do not claim attachments exist unless verified.
Lessons learned
- A browser failure does not by itself prove that the workstation has no internet connectivity.
- Diagnostic results should be recorded separately from assumptions about the cause.
- A disabled feature is not necessarily a misconfiguration.
- The customer's permissions and a support technician's authorized permissions can differ.
- An evidence-based escalation is a valid Tier 1 outcome.
- Clear handoff notes should include unresolved findings as well as the leading hypothesis.
Lab status
The exercise ended at preparation for escalation. Proxy restoration, resolution of the DNS timeout, and reversal of temporary lab networking changes were not confirmed. They are not presented as completed work.
