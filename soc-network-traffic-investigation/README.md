
# SOC Network Traffic Investigation

A cybersecurity lab investigation using Wireshark, VirusTotal and MISP to examine suspicious network traffic, document indicators and support SOC triage.

## Project Overview

This project analyzes a publicly available packet capture from Malware-Traffic-Analysis.net in a simulated enterprise environment.

The investigation follows suspicious DNS, TLS and HTTP activity, correlates network indicators with threat intelligence, and documents the findings in MISP.

## Tools Used

- **Wireshark:** Packet inspection and traffic analysis.
- **VirusTotal:** Indicator reputation and infrastructure enrichment.
- **MISP:** Structured threat intelligence documentation.
- **MITRE ATT&CK:** Assessment of potential adversary techniques.

## Investigation Workflow

1. Validate the initial alert against packet evidence.
2. Identify the affected workstation and suspicious destinations.
3. Analyze DNS queries, TLS connections and HTTP requests.
4. Build an incident timeline.
5. Enrich indicators using threat intelligence.
6. Record indicators in MISP.
7. Recommend further investigation and response actions.

## Key Findings

- The investigated workstation resolved a suspicious domain and contacted the associated IP address.
- TLS and HTTP traffic connected the workstation to the same external infrastructure.
- Repeated HTTP requests targeted the `/api/set_agent` endpoint.
- An HTTP response contained a ZIP archive error.

## Limitations

The observed traffic supports further investigation but does not establish successful malware execution, persistence, credential theft or data exfiltration. Endpoint evidence would be needed to confirm those outcomes.


## Data Source

The packet capture comes from the **Lumma in the Room-ah** traffic analysis exercise on [Malware-Traffic-Analysis.net](https://www.malware-traffic-analysis.net/).

This project documents a lab investigation using public exercise data.
