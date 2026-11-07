# HawkEye — Network Forensics Investigation

> **Status: Draft pending evidence validation.** This case study is based on the analyst's supplied lab notes. The original PCAP and supporting screenshots were not attached for this draft, so the recorded answers and observations have not been independently checked against the capture.

## Overview

This project documents an investigation of the CyberDefenders HawkEye network-forensics lab. In the scenario, an accountant receives an invoice email with a download link. Suspicious network activity follows, and the analyst reviews a packet capture to trace the download and suspected data exfiltration.

The investigation covers packet-capture metadata, endpoint and DNS analysis, HTTP object review, malware-file identification, and SMTP traffic associated with exfiltration.

## Investigation workflow

1. Review capture properties and packet count in Wireshark.
2. Set packet time display to UTC and record the first-packet time and capture duration.
3. Review Ethernet and IP endpoints to identify active systems and internal hosts.
4. Inspect DHCP, DNS, HTTP, and SMTP traffic to follow the suspected activity.
5. Identify the downloaded object and calculate its MD5 locally.
6. Correlate the HTTP and SMTP records with the reported exfiltration details.
7. Record conclusions with a clear distinction between packet evidence and external reputation or geolocation lookups.

## Tools and techniques recorded

- Wireshark: capture properties, endpoints, display filters, protocol details, HTTP object export, and TCP stream review.
- PowerShell `Get-FileHash` with the MD5 algorithm: hash calculation for the exported object.
- External MAC-vendor and IP-geolocation lookup services: contextual enrichment, which should be dated and cited in the final report.

## Preliminary findings from the supplied notes

- The capture is reported to contain **4,003 packets**, beginning **2019-04-10 20:37 UTC**, with an elapsed duration of **01:03:41**.
- The most active link-layer endpoint is reported as `00:08:02:1c:47:ae`, identified in the notes as Hewlett-Packard.
- DHCP hostname evidence is reported as `Beijing-5cd1-PC`.
- The invoice domain is reported as `proforma-invoices.com`, resolving in the capture to `217.182.138.150`.
- The downloaded object is reported as `tkraw_Protected99.exe`, with MD5 `71826BA081E303866CE2A2534491A2F7`.
- SMTP traffic is reported to contain an exfiltration email addressed to `sales.del@macwinlogistics.in`; the notes identify the malware variant as Reborn V9.

These are **reported findings**, not independently verified findings, until checked against the PCAP and saved evidence.

## Validation notes

- For the `/24` host count, count distinct internal IPv4 addresses in the organization's actual private `/24` subnet. The supplied explanation about an address octet being “less than 24” is not a valid way to identify hosts in a `/24` network.
- Identify the DNS server from the IP-layer endpoints and DNS request/response direction. Ethernet destination MAC alone does not establish the DNS server's IP.
- Match DNS answers by query name and transaction, and preserve the relevant frame numbers in the evidence log.
- Treat IP geolocation and NIC-vendor results as external enrichment, not facts established by the PCAP alone.
- Recheck every credential against the capture. Do not publish recovered passwords in the public portfolio; the public report should redact them.

## Evidence to add before publication

- PCAP filename and source/platform details.
- Screenshots or exported evidence for capture properties, endpoints, DHCP hostname, DNS query/response, HTTP download, and SMTP stream.
- Hash record for the exported file, including the exact local filename and command used.
- Dated citations for vendor, headquarters, and IP-geolocation enrichment.
- Final PDF report after the validation notes above are resolved.

## Safety and scope

This is a controlled training-lab investigation. The report should present only observations supported by the capture and should not expose recovered passwords or other reusable secrets in a public repository.
