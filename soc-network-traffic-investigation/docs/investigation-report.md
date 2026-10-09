# Network Traffic Investigation Report

## Executive Summary

This investigation examines suspicious network activity in a publicly available packet capture using Wireshark, VirusTotal and MISP.
The analysis identified communication between workstation `10.1.21.58` and external infrastructure associated with `whitepepper[.]su` and `153.92.1[.]49`. Evidence included DNS resolution, a TLS Client Hello and repeated HTTP requests to `/api/set_agent`.
The findings support further investigation. They do not confirm successful malware execution, persistence, credential theft or data exfiltration.

## Investigation Context

The packet capture was obtained from the **Lumma in the Room-ah** exercise published by Malware-Traffic-Analysis.net.
The enterprise setting and SOC response workflow are simulated. The initial alert described in the source material concerns suspected Lumma Stealer victim-fingerprinting activity.
The malware association provides investigative context; it does not independently prove that the endpoint was compromised.

## Tools and Methods

| Tool or framework | Purpose |
| Wireshark | Analyze DNS, TCP, TLS and HTTP traffic |
| VirusTotal | Enrich domain and IP indicators |
| MISP | Record indicators and supporting context |
| MITRE ATT&CK | Assess potential technique associations |

Analysis focused on correlating packet observations, reconstructing the event sequence and separating confirmed observations from hypotheses.

## Investigated Host

| Attribute | Value |
| Workstation IP | `10.1.21.58` |
| Internal DNS server | `10.1.21.2` |
| Hostname recorded in the investigation | `DESKTOP-ES9F3ML` |
| Network segment | `10.1.21.0/24` |
| External destination | `153.92.1[.]49` |
| Associated domain | `whitepepper[.]su` |
| Destination ports observed | TCP/443 and TCP/80 |

External indicators are defanged in this report to prevent accidental navigation.

## Packet Analysis

### DNS Resolution

Frame `23721` shows the workstation querying the internal DNS server for `whitepepper[.]su`.
Frame `23723` contains the response associating that domain with `153.92.1[.]49`.
These observations establish the domain-to-IP relationship during the captured activity.

### TLS Connection

Following DNS resolution, the workstation initiated a TCP connection to the external IP on port 443.
Frame `23728` contains a TLS Client Hello with the domain in the Server Name Indication field. This links the TLS connection attempt to the preceding DNS activity.
A Client Hello alone does not establish that the TLS session completed successfully.

### HTTP Requests

Separate HTTP communication occurred on TCP port 80.
Frame `24601` contains a POST request to `/api/set_agent`. The documented request includes identification and token parameters, alongside fields such as `agent=Chrome` and `act=log`.
Further requests to the same endpoint indicate repeated interaction with the external service. These requests are relevant to the investigation but do not independently prove credential theft or malware execution.

### HTTP Response and Archive Error

Frame `24616` contains an HTTP `200 OK` response whose body reports an error opening a ZIP archive.
The HTTP status indicates that a response was returned; it does not demonstrate successful archive delivery or execution.
No malicious file was recovered during the documented analysis, and no payload hash was calculated.

## Incident Timeline

Times below are recorded in UTC in the original investigation.

| Time | Frame | Observation |
| 23:05:36.218945 | 23721 | Workstation queries the suspicious domain |
| 23:05:36.253143 | 23723 | DNS response returns the external IP |
| 23:05:36.253555 | 23724 | Workstation initiates TCP connection to port 443 |
| 23:05:36.432084 | 23728 | TLS Client Hello includes the domain in SNI |
| 23:05:40.287894 | 24601 | HTTP POST request to `/api/set_agent` |
| 23:05:40.605291 | 24616 | HTTP response contains an archive error |
| 23:05:47.463142 | 25286 | Another HTTP POST request to the same endpoint |

The sequence connects DNS resolution with subsequent TLS and HTTP activity. It does not establish successful compromise.

## Network Indicators

| Indicator | Type | Supporting evidence |
| `whitepepper[.]su` | Domain | DNS queries, TLS SNI and HTTP requests |
| `153.92.1[.]49` | IPv4 address | DNS response and subsequent connections |
| `hxxp://whitepepper[.]su/api/set_agent` | URL | Repeated HTTP requests |
| `/api/set_agent` | HTTP path | Application-level traffic to the identified destination |

The HTTP path should be interpreted alongside destination and behavioral evidence. A path by itself is not a reliable indicator of malicious activity.

## Threat Intelligence Assessment
VirusTotal enrichment in the original investigation reported malicious detections for the domain and a smaller number for the IP address.
Exact detection counts are omitted because the original report records inconsistent domain totals. Any published count should be paired with its supporting screenshot and lookup date.
The enrichment increases the relevance of the observed network activity but does not replace endpoint evidence or establish attribution.

## MITRE ATT&CK Assessment

**T1071.001 — Application Layer Protocol: Web Protocols** is a candidate mapping based on the repeated HTTP communication, if its command-and-control purpose is established.

HTTP traffic alone does not confirm command and control.
The available evidence is insufficient to confirm system discovery, credential access, persistence, lateral movement or exfiltration techniques.

## MISP Documentation

The investigation recorded the external IP, domain and URL as attributes in a MISP event.
The documented event used a High threat level and a “This community only” distribution setting. Those settings describe how the lab event was classified and shared; they do not prove compromise or authorize public redistribution of an export.
The event’s Completed analysis status describes completion of the documented analysis, not confirmation that all incident questions were resolved.

## Proposed SOC Triage Workflow

1. **Validate:** Compare the initial alert with packet addresses, protocols and timestamps.
2. **Investigate:** Correlate DNS resolution, TLS activity and HTTP requests.
3. **Enrich:** Review indicator reputation and historical infrastructure relationships.
4. **Prioritize:** Assess the suspicious behavior alongside host context and available evidence.
5. **Escalate:** Request deeper endpoint investigation when the evidence warrants it.
6. **Respond:** Apply proportionate containment and monitoring actions based on validated findings.

## Recommended Response Actions

These actions are recommendations for the simulated scenario, not actions demonstrated as completed.

- Preserve the packet capture and relevant host, DNS, proxy and security logs.
- Investigate endpoint processes, browser activity and network connections around the observed timestamps.
- Search other systems for communication with the same infrastructure.
- Consider blocking or monitoring the indicators after reviewing context and potential impact.
- Isolate the endpoint if additional evidence supports compromise.
- Reset affected credentials and revoke sessions if exposure is identified.
- Update threat intelligence records as new evidence becomes available.

## Limitations

This report is based on the documented packet analysis and associated enrichment.

The evidence does not confirm:

- Successful malicious-file download or execution.
- Persistence on the workstation.
- Credential theft or session-token compromise.
- Lateral movement.
- Data exfiltration.

Additional endpoint telemetry and corroborating logs are required to resolve these questions.

## Conclusion

The documented traffic links workstation `10.1.21.58` to suspicious external infrastructure through DNS, TLS and repeated HTTP activity.

The evidence supports a prioritized investigation while leaving the endpoint’s compromise status unresolved.

## Contributions

## References

- [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/) — Source exercise and packet capture.
- [Wireshark](https://www.wireshark.org/) — Network protocol analysis.
- [VirusTotal](https://www.virustotal.com/) — Indicator enrichment.
- [MISP Documentation](https://www.misp-project.org/documentation/) — Threat intelligence documentation.
- [MITRE ATT&CK T1071.001](https://attack.mitre.org/techniques/T1071/001/) — Web Protocols.
