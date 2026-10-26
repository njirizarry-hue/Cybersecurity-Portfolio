
# SOC Investigation 001: Suspected SSH Brute-Force Activity

**Platform:** Splunk  
**Log Source:** Linux mail server authentication logs  
**Investigation Type:** Authentication Monitoring / Threat Detection  
**Environment:** Simulated training lab  
**Status:** Initial analysis completed

---

## 1. Executive Summary

This investigation examined authentication logs from a simulated Linux mail server to identify suspicious login attempts targeting the root account.

Using Splunk Search Processing Language (SPL), I searched authentication events for failed login activity and examined indicators of potential SSH brute-force attempts.

After completing the guided portion of the lab, I expanded the investigation independently by extracting source IP addresses and counting associated events to identify potential repeated sources.

## 2. Investigation Objectives

- Identify failed authentication attempts targeting the root account.
- Examine evidence suggesting SSH-based login attempts.
- Extract source IP addresses from matching log events.
- Count and rank events associated with each source IP.
- Assess whether the observed activity warrants further investigation.

## 3. Lab Environment

| Component | Description |
|---|---|
| SIEM | Splunk Cloud |
| Data source | Practice Linux mail server security logs |
| Log type | Authentication / SSH-related events |
| Query language | Splunk SPL |
| Data environment | Simulated training dataset |

## 4. Investigation Methodology

### Step 1: Search for Failed Root Authentication

After importing the practice log data into Splunk, I began searching for failed authentication events associated with the root account.

The initial search used the main index, a host filter, and failure-related keywords.

```spl
index=main host=mailsv fail* root
```

**Analysis:**

- `index=main` searches the specified Splunk index.
- `host=mailsv` restricts the search to the selected host.
- `fail*` searches for matching failure-related terms.
- `root` searches for events containing the root account name.

The results showed repeated authentication failures involving the root account.

### Step 2: Identify Potential SSH Activity

While reviewing the matching events, I noticed SSH-related indicators, including references to `sshd` and port 22.

These observations suggested that the failed authentication events involved attempts to access the server through SSH.

The repeated failures raised the possibility of a brute-force attempt, although additional validation would be required to confirm the attack pattern.

### Step 3: Expand the Investigation

After completing the guided lab, I wanted to determine whether the activity originated from repeated source IP addresses.

I researched how to extract IP addresses from raw Splunk events using the `rex` command and regular expressions.

I then used `stats` to count matching events by source IP and `sort` to arrange them in descending order.

```spl
index=main fail*
| rex field=_raw "from (?<src_ip>\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})"
| stats count by src_ip
| sort -count
```

**Query explanation:**

- `rex` extracts source IP addresses from matching raw log events.
- `stats count by src_ip` counts matching events associated with each extracted IP.
- `sort -count` lists source IPs from highest to lowest event count.

This provided a way to prioritize which source IP addresses warranted closer investigation.

**Important limitation:** This expanded search includes matching failure-related events across the main index rather than only the original host and root account. Its results should not automatically be interpreted as counts of failed root SSH logins.

## 5. Findings

The investigation identified:

- Repeated failed authentication events involving the root account.
- SSH-related indicators in the authentication logs.
- A method to extract and summarize source IP addresses.
- A way to rank source IPs by frequency of matching events.

These findings support further investigation of potentially automated authentication attempts.

The available documentation does not establish whether an attacker successfully authenticated or compromised the server.

## 6. Recommended Follow-Up Investigation

In a production SOC environment, my next steps would be to:

1. Review source IP addresses with the highest numbers of failed attempts.
2. Determine whether failures occurred within a short time window.
3. Check for successful logins following repeated failures.
4. Review additional activity associated with suspicious sources.
5. Escalate according to the organization's incident response procedures if the evidence warrants it.

## 7. Lessons Learned

This lab helped me become more familiar with Splunk and its Search Processing Language.

The most valuable part was extending the investigation beyond the original instructions. Researching regular expressions and event aggregation helped me understand how raw authentication data can be transformed into information useful for security analysis.

I also learned the importance of distinguishing suspicious patterns from confirmed security incidents.

## 8. Evidence

Screenshots from the original Splunk lab will be added here to demonstrate the search process, observed authentication events, and investigation results.

## 9. Training Disclosure

This investigation was conducted using simulated laboratory data for educational purposes. It does not represent a production security incident.
