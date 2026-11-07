# HawkEye Network Forensics Investigation Report

**Status:** Draft — requires validation against original PCAP and evidence before submission  
**Analyst:** Abhinav Pratap Singh  
**Environment:** CyberDefenders HawkEye lab  
**Primary tool recorded:** Wireshark

## 1. Executive summary

The HawkEye lab scenario concerns an accountant who receives an invoice email containing a download link. The supplied solve notes describe analysis of a packet capture to identify the involved hosts, resolve the invoice domain, inspect a downloaded executable, and follow SMTP traffic associated with suspected exfiltration.

The notes report a download named `tkraw_Protected99.exe` and SMTP traffic addressed to `sales.del@macwinlogistics.in`. They also report that a decoded SMTP data stream identified the malware variant as Reborn V9. These conclusions remain provisional because the source PCAP, screenshots, exported object, and decoded stream were not attached for this review.

## 2. Scope and evidence basis

This draft is based only on the analyst-provided walkthrough and answers. It does not claim an independent re-analysis of the PCAP. Packet numbers, timestamps, addresses, hashes, and protocol observations below should be checked against the original capture before this report is submitted or published.

### Evidence currently described in the notes

- A Wireshark capture reported to contain 4,003 packets.
- Capture start time and elapsed duration viewed in Wireshark.
- Endpoint and DHCP observations.
- DNS query and answer associated with `proforma-invoices.com`.
- HTTP response in frame 3155 containing the reported executable.
- SMTP activity beginning around frame 3175, including recipient information and an encoded message stream.
- A locally exported executable whose MD5 was calculated in PowerShell.

### Evidence not provided with this draft

- The original PCAP.
- Screenshots or packet exports for the observations above.
- The exported executable or a hash-record screenshot/output.
- The full SMTP stream or decoded content used to identify the variant.
- Dated source citations for manufacturer and geolocation lookups.

## 3. Investigation method

The supplied notes describe the following workflow:

1. Read the packet count from Wireshark's status bar.
2. Change the time display to UTC and read the first packet timestamp.
3. Read elapsed capture time from **Statistics → Capture File Properties**.
4. Review **Statistics → Endpoints** and sort packet counts to find active Ethernet and IP endpoints.
5. Inspect DHCP Option 12 for the host name.
6. Filter DNS traffic and inspect the query and matching response for the invoice domain and resolved address.
7. Inspect the HTTP response around frame 3155 and export the object using **File → Export Objects → HTTP**.
8. Calculate the exported file's MD5 hash with `Get-FileHash -Algorithm MD5`.
9. Inspect SMTP traffic and follow the TCP stream to review the reported exfiltration content.

The exact display filters, frame references, timestamps, and outputs should be added from the original capture and evidence screenshots.

## 4. Findings recorded in the analyst's notes

| Question area | Reported result | Validation needed |
|---|---|---|
| Capture size | 4,003 packets | Confirm status bar or capture properties screenshot |
| First captured time | 2019-04-10 20:37 UTC | Confirm full timestamp and timezone display |
| Capture duration | 01:03:41 | Confirm capture file properties |
| Most active link-layer endpoint | `00:08:02:1c:47:ae` | Confirm endpoint row and packet count |
| NIC manufacturer | Hewlett-Packard | Record lookup source and date |
| Manufacturer headquarters | Palo Alto | Confirm company/entity and cite source |
| Internal hosts in organization `/24` | 3 (reported) | **Recalculate.** Count unique internal IPs in the actual private `/24`; “octet less than 24” is not a valid test |
| DHCP hostname | `Beijing-5cd1-PC` | Confirm DHCP Option 12 frame |
| DNS server | `10.4.10.4` (reported) | **Recheck using IP-layer endpoints and DNS direction.** Ethernet MAC destination by itself does not identify the DNS server IP |
| Domain queried in packet 204 | `proforma-invoices.com` | Confirm packet 204 and query details |
| Domain address | `217.182.138.150` | Confirm matching DNS response and transaction |
| Country for domain address | France (reported) | Cite dated geolocation lookup; external enrichment can vary |
| Victim OS string | Windows NT 6.1 (reported) | Preserve exact User-Agent from the relevant HTTP request |
| Downloaded file | `tkraw_Protected99.exe` | Confirm HTTP request URI and exported object |
| File MD5 | `71826BA081E303866CE2A2534491A2F7` | Recalculate from the same exported file and retain output |
| Web server | LiteSpeed (reported) | Confirm HTTP `Server` header spelling/value |
| Victim public IP | `173.66.146.112` (reported) | Identify the exact packet/field and direction supporting attribution |
| Email server country | United States (reported) | Cite dated lookup for the destination mail server IP |
| SMTP server software | Exim 4.91 (reported) | Confirm server greeting or response near frame 3175 |
| SMTP recipient | `sales.del@macwinlogistics.in` | Confirm `RCPT TO` in the stream; redact if needed for public posting |
| SMTP password | Recovered in supplied notes | **Redacted from this public draft.** Verify privately against the capture; do not publish the plaintext secret |
| Malware variant | Reborn V9 (reported) | Attach the relevant stream excerpt and decoding steps; distinguish decoded label from independent malware analysis |
| Bank of America credentials | Recovered in supplied notes | **Redacted from this public draft.** Do not publish plaintext credentials |
| Exfiltration interval | 10 minutes (reported) | Show timestamps of at least two matching exfiltration events and how the interval was calculated |

## 5. Preliminary analysis

The supplied notes describe a chain in which a user visits an invoice-related domain, downloads an executable over HTTP, and the compromised system later sends data through SMTP. The notes attribute the downloaded file and SMTP activity to a malware incident and identify Reborn V9 from an encoded SMTP stream.

For a defensible report, separate direct packet observations from interpretation:

- **Direct observations:** packet/frame numbers, protocol fields, addresses, HTTP URI, response headers, SMTP commands, and timestamps.
- **Derived values:** file hash, capture duration, host count, and exfiltration interval.
- **External enrichment:** NIC vendor, organization headquarters, and IP geolocation.
- **Assessment:** attribution of the behavior to malware or an exfiltration operation.

The report should not call a value verified until the corresponding packet, exported object, calculation, or dated reference is retained as evidence.

## 6. Credential handling

The lab notes include an SMTP password and banking access credentials. They are intentionally omitted from this public-facing draft. If the internship rubric requires documenting recovered credentials, include them only in a private submission artifact if permitted, or mask them while documenting the exact packet evidence and recovery method. Do not publish reusable secrets in a public GitHub repository.

## 7. Preliminary defensive recommendations

Subject to confirmation against the PCAP, the scenario supports reviewing:

- Email filtering and user reporting for invoice-themed links and unexpected executable downloads.
- Endpoint controls that block or quarantine untrusted executables.
- DNS and proxy monitoring for newly observed or suspicious domains.
- Egress controls and alerting for unexpected SMTP traffic from user workstations.
- Detection for repeated outbound email activity and periodic transfers that may indicate automated exfiltration.
- Credential rotation and incident response steps when credentials are present in captured traffic.
- Preservation of PCAPs, extracted objects, hashes, and analyst notes with timestamps and provenance.

## 8. Conclusion

The analyst's notes describe a useful network-forensics investigation covering capture review, DNS and HTTP analysis, object extraction, hash calculation, and SMTP stream inspection. The current draft is not yet submission-ready because the original PCAP and supporting evidence were not available for validation. The internal `/24` host count and DNS-server identification method need particular attention; external enrichment and credential-related findings also need careful documentation and redaction.

## 9. Evidence checklist before final PDF

- [ ] Confirm the packet count, complete UTC timestamp, and elapsed duration.
- [ ] Save the endpoint table showing the busiest MAC and internal IPs.
- [ ] Recalculate the count of distinct internal hosts in the correct `/24` subnet.
- [ ] Confirm DNS server IP using IP-layer fields and request/response records.
- [ ] Save frames for DHCP hostname, packet 204 query, and the corresponding DNS answer.
- [ ] Save the HTTP request/response and exported object metadata.
- [ ] Recalculate the MD5 from the exported object and retain the result.
- [ ] Save SMTP greeting, `RCPT TO`, relevant message data, and timestamps supporting the interval.
- [ ] Cite dated enrichment sources for vendor and geolocation details.
- [ ] Keep recovered passwords out of public materials.
- [ ] Render and visually review the final PDF before submission.
