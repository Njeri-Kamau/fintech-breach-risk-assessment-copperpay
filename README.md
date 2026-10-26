 # Fintech Security Architecture & ISO 27001 Risk Assessment: The CopperPay Incident

## Executive Summary
This repository documents an end-to-end cybersecurity risk assessment, threat analysis, and architectural review for **CopperPay**, a high-volume mobile payment provider operating in Zambia serving over 1.2 million active users. The investigation dissects a multi-stage security incident involving social engineering (smishing), lateral privilege escalation via shared credentials, bulk data scraping across unauthenticated cloud APIs, and sustained micro-transaction manipulation.

By mapping discovered vulnerabilities and systemic operational gaps to the **ISO/IEC 27001:2022 Annex A** security controls, this project establishes a defensible security architecture, a granular threat register, and an actionable zero-trust remediation roadmap designed for modern financial technology ecosystems.

---

## Target Profile & Architecture Overview

CopperPay bridges financial access across rural and cross-border commercial corridors by providing USSD and SMS-based transaction channels for users on feature phones, alongside web dashboards for merchant settlements.

* **Target Organization:** CopperPay (Mobile Money & Digital Payments)
* **User Scale:** 1.2M+ registered mobile accounts across multi-regional trade routes
* **Infrastructure Model:** Hybrid cloud architecture
  * *On-Premises Data Center:* Hosts the core transactional ledger, SMS/USSD gateways, and database records.
  * *Public Cloud Nodes:* Hosts third-party partner integration endpoints, business analytics, and staging environments.
* **Key Operational Vulnerabilities Identified:**
  * Direct-to-production unreviewed deployments bypassing change approval boards (CAB).
  * Departmental staff utilizing shared administrative logins and identical passwords across support and analytics tools.
  * Unauthenticated, rate-unlimited legacy testing endpoints left online after migration sprints.
  * Uncontrolled shadow data storage containing customer records across departmental spreadsheets.

---

## Anatomy of the Incident: Attack Chain Analysis

The incident did not rely on brute-force network penetration, but rather exploited trust relationships, identity governance gaps, and abandoned endpoints.

### Phase 1: Reconnaissance
Threat actors monitored public social media customer support interactions, identifying recurring transaction inquiry patterns, customer dispute phrasing, and the specific telecommunications provider routing the official payment shortcode.

### Phase 2: Credential Harvesting via Targeted Smishing
Posing as network engineers from the primary telecommunications provider, attackers sent an urgent SMS notification to customer support staff. The alert claimed the corporate transaction shortcode faced immediate deactivation within 24 hours unless administrative account verification was completed immediately. An employee followed the external link and submitted corporate portal credentials into a deceptive login gateway.

### Phase 3: Lateral Movement via Shared Credentials
Because operational teams shared administrative logins to accelerate daily workflows, the compromised credentials permitted immediate access to customer profile portals. Furthermore, the identical password unlocked the public cloud analytics environment, enabling unauthorized traversal across infrastructure boundaries.

### Phase 4: Bulk Data Scraping on Neglected Endpoints
Within the public cloud analytics environment, legacy testing APIs created during historical payment gateway migrations remained online and completely unmonitored. These testing endpoints lacked authentication requirements and request throttling, allowing automated scripts to silently extract full profile records for over 300,000 users.

### Phase 5: Fractional Micro-Transaction Fraud
Because automated fraud detection alarms monitored only transactions exceeding standard transfer ceilings, attackers systematically altered recurring micro-transaction values over eight weeks. The financial loss was identified through merchant balance reconciliation disputes rather than internal intrusion detection mechanisms.

---

## Threat and Risk Register

| Risk ID | Vulnerability Description | Threat Vector | Business Impact | Inherent Rating | Existing Baseline Control | Recommended Mitigating Control |
| :--- | :--- | :--- | :--- | :---: | :--- | :--- |
| **SEC-01** | Sub-threshold transaction fraud monitoring | Automated micro-siphoning below static alarm limits | Cumulative financial capital loss | **High** | High-value transaction thresholds only | Dynamic transaction velocity checks, anomaly baselining, automated SMS reconciliation |
| **SEC-02** | Shared credentials and missing MFA | Phishing-induced credential reuse and lateral traversal | Complete account takeover without attribution | **Critical** | Single-factor static passwords | Deprecate shared logins, enforce hardware or software MFA, implement granular RBAC |
| **SEC-03** | Exposed legacy staging and testing APIs | Unauthenticated mass data scraping | Breach of 300K+ customer records and statutory penalties | **Critical** | Unauthenticated endpoints | Centralized API gateway, OAuth2 token validation, strict IP allowlisting and rate throttling |
| **SEC-04** | Shadow data sprawl in unmanaged formats | Distributed unencrypted customer spreadsheets | Severe compliance fines and regulatory sanctions | **High** | Informal departmental storage | Data minimization policies, mandatory cryptographic protection at rest, automated data loss prevention |
| **SEC-05** | Direct, unreviewed production deployments | CI/CD pipeline bypass introducing hidden vulnerabilities | Long dwell-time compromises and destabilized services | **High** | Ad-hoc terminal pushes | Enforced DevSecOps pipelines, strict separation of duties, mandatory peer approval gates |

---

## Strategic Governance: ISO/IEC 27001:2022 Control Mapping

To systematically address these vulnerabilities, security controls are aligned directly with the **ISO/IEC 27001:2022** standard:

### 1. Organizational Controls
* **A.5.1 Policies for Information Security:** Formulate and enforce organizational policies governing API deprecation lifecycles, remote administration, and credential uniqueness across all business functions.
* **A.5.9 Inventory of Information Assets:** Establish a centralized, automated asset register cataloging all production and staging web endpoints, microservices, cloud buckets, and public IP ranges with designated system custodians.
* **A.5.24 Incident Management Planning:** Construct formal Cyber Incident Response Team (CIRT) playbooks establishing deterministic internal containment steps, forensic preservation, and statutory breach notification timelines.

### 2. People & Cultural Controls
* **A.6.3 Information Security Awareness Training:** Execute continuous, role-based awareness campaigns covering smishing identification, domain verification, and reporting protocols for support teams and field representatives.

### 3. Technological & Architecture Controls
* **A.8.1 & A.8.3 Privileged Access & Secure Authentication:** Deploy centralized Identity and Access Management (IAM), decommission shared accounts in favor of individual identity auditing, and enforce multi-factor authentication (MFA) across all endpoints.
* **A.8.2 Information Access Restriction:** Enforce OAuth2 and JSON Web Token (JWT) authorization on all internal and external API routes, backed by automated token expiration and per-client rate limiting.
* **A.8.4 & A.8.5 Continuous Logging & Threat Monitoring:** Ingest transactional logs, authentication attempts, and API telemetry into a centralized SIEM (Wazuh/Elasticsearch) with automated alerting for velocity anomalies and credential stuffing.
* **A.8.6 Secure Engineering Principles:** Enforce strict environment isolation (Development, Staging, Production) with branch protection rules, mandatory peer code reviews, and automated SAST/DAST pipeline gates.

---

## Operational Remediation Roadmap

### Immediate Containment (0 to 30 Days)
* Terminate all active administrative sessions and enforce mandatory individual password resets across all operational teams.
* Implement multi-factor authentication (MFA) across all administrative portals, internal tools, and cloud management consoles.
* Decommission and isolate all unauthenticated testing endpoints and purge sensitive staging datasets from cloud analytics nodes.
* Implement velocity-based anomaly rules for low-value recurring transactions to stop automated micro-siphoning.

### Medium-Term Hardening (30 to 90 Days)
* Deploy an enterprise API gateway requiring cryptographic token verification and strict rate throttling across all endpoints.
* Channel infrastructure and transaction telemetry into a centralized SIEM platform for real-time behavioral monitoring.
* Formalize and rehearse incident response runbooks to streamline operational containment and merchant communications.
* Audit and remediate departmental network drives to eliminate unencrypted customer data exports.

### Long-Term Institutionalization (90 to 180 Days)
* Complete institutional alignment with the ISO/IEC 27001:2022 Information Security Management System (ISMS) framework.
* Embed automated CI/CD DevSecOps pipelines with mandatory peer reviews and vulnerability scanning prior to production release.
* Establish recurring third-party penetration testing and threat modeling schedules to evaluate evolving attack surfaces.

---

## Author & Contact Information

* **Security Analyst:** Eunice Njeri Kamau
 
