# NIST Policy Analysis: Risk Assessment and Access Control

**Project type:** Cybersecurity training exercise  
**Focus:** Governance, risk management, and access control  
**Framework reference:** NIST Cybersecurity Framework (CSF) 1.1

## Overview

In this training exercise, I analyzed risk assessment and access control policies from two public-sector organizations. I examined responsibilities, review requirements, and opportunities to make policy compliance easier to measure.

This was a document-based analysis. I did not test the organizations' systems or verify how effectively their controls operated.

## Framework Context

This writeup uses CSF 1.1 identifiers to match the original exercise. CSF 2.0 reorganized the framework, including moving the PR.AC category into PR.AA.

The North Carolina policy explicitly uses the NIST SP 800-53 Access Control family. CSF references below provide conceptual connections; they are not a claim that CSF supplied the policy template.

## Part 1: Risk Assessment

**Organization:** County of San Bernardino Department of Behavioral Health  
**Policy:** IT5007 — Risk Assessment Policy

### Observations From My Original Lab

My notes identified assessments triggered by new systems or significant infrastructure changes, alongside periodic reviews. They also described reporting assessment results to the Privacy Officer and Security Officer.

These requirements illustrate how an organization can assign responsibility and revisit risk when its environment changes.

**Source limitation:** The original PDF link could not be retrieved during preparation of this portfolio entry. These observations reflect my original lab notes, rather than a newly verified policy edition.

### Relevant CSF 1.1 Outcomes

The descriptions below summarize the framework in my own words.

| Reference | Relevant outcome |
| --- | --- |
| ID.RA-1 | Record vulnerabilities affecting assets |
| ID.RA-2 | Obtain cyber threat intelligence from external sources and sharing communities |
| ID.RA-3 | Document internal and external threats |
| ID.RA-4 | Identify potential business consequences and their likelihood |
| ID.RA-5 | Combine threat, vulnerability, likelihood, and impact information to evaluate risk |
| ID.RA-6 | Identify and prioritize risk responses |

These outcomes provide an analysis structure; the notes do not establish that the policy satisfies every outcome.

### Recommendations

My proposed improvements were to:

- Specify a recurring assessment interval if related procedures do not already establish one.
- Use a consistent method for scoring risk.
- Assign remediation deadlines based on severity.
- Document how adherence will be checked.

These are recommendations for clarification and follow-through, not verified findings of missing organizational controls.

## Part 2: Access Control

**Organization:** State of North Carolina  
**Policy:** SCIO-SEC-301 — Access Control Policy  
**Verified reference edition:** Version 4, reviewed March 26, 2025

### Documented Review Requirements

Page 3 specifies different intervals for different kinds of access.

| Access category | Review interval |
| --- | --- |
| Standard user rights | At least annually |
| Administrator or elevated accounts | Every six months |
| Employee badges for general physical access | Annually |
| Contractor, vendor, or other nonemployee badges for general physical access | Quarterly |
| Secure-area badges where FTI/SSA data is not present | Quarterly |
| Secure-area badges where FTI/SSA data is present | Monthly |

FTI means Federal Tax Information; SSA refers to the Social Security Administration. Secure-area intervals apply regardless of employee type.

### Relevant CSF 1.1 Connections

- **PR.AC-1:** Management of identities and credentials throughout their lifecycle.
- **PR.AC-2:** Protection and management of physical access.
- **PR.AC-4:** Permission management using least privilege and separation of duties.

### Recommendations

My initial recommendations included access audits, role-based validation, alerts, and leadership reporting. Because the policy already requires reviews, these should be treated as ways to support implementation rather than entirely new requirements.

Possible measures include review completion rates and the number of overdue access corrections. These are proposed measures, not results I collected.

## Key Takeaways

- Policies translate broad security outcomes into assigned responsibilities and review schedules.
- Account access, physical access, and policy-document reviews are different activities.
- Framework mappings need the correct version and identifiers.
- Written requirements alone do not demonstrate effective implementation.
- Recommendations should distinguish an unclear requirement from a confirmed control gap.

## References

- [NIST CSF Version 1.1](https://doi.org/10.6028/NIST.CSWP.04162018)
- [NIST CSF transition FAQs](https://www.nist.gov/cyberframework/faqs)
- [North Carolina Access Control Policy](https://it.nc.gov/documents/statewide-policies/scio-access-control/download?attachment) — Version 4, page 3 for review intervals.
- [San Bernardino IT5007 original source link](https://wp.sbcounty.gov/dbh/wp-content/uploads/2016/08/IT5007.pdf) — retained from the original lab; retrieval unavailable during portfolio preparation.
- [San Bernardino Standard Practice Manual](https://wp.sbcounty.gov/dbh/forms/standard-practice-manual/) — lists IT5007.

[Return to portfolio](../../README.md)
