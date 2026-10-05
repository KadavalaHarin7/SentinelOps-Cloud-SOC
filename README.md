&#x20;SentinelOps — Cloud Identity Threat Detection \& Incident Response



SentinelOps is a Microsoft Sentinel lab I built to work through cloud identity

detection and incident handling.



I used Microsoft Entra ID logs as the main telemetry source, wrote KQL

detections in Microsoft Sentinel, investigated the resulting incidents, and

connected Sentinel to ServiceNow through an Azure Logic App.



&#x20;What I worked on



\- Configured Microsoft Entra AuditLogs and SigninLogs for Sentinel

\- Built KQL detections for cloud identity activity

\- Investigated an OAuth administrative consent event

\- Built and tuned an unusual cloud sign-in detection

\- Created Sentinel incidents from detection rules

\- Connected Sentinel incidents to ServiceNow using Azure Logic Apps

\- Added correlation-based checking to avoid duplicate ServiceNow incidents

\- Tested both the create and update paths

\- Verified that existing ServiceNow work notes were preserved during updates



&#x20;Architecture



Microsoft Entra ID

&#x20;     │

&#x20;     ├── AuditLogs

&#x20;     └── SigninLogs

&#x20;            │

&#x20;            ▼

&#x20;   Log Analytics Workspace

&#x20;            │

&#x20;            ▼

&#x20;    Microsoft Sentinel

&#x20;            │

&#x20;            ▼

&#x20;      KQL Analytics Rules

&#x20;         │        │

&#x20;         │        └── Unusual Sign-In Context

&#x20;         │

&#x20;         └────────── OAuth Administrative Consent

&#x20;                        │

&#x20;                        ▼

&#x20;                Sentinel Incident

&#x20;                        │

&#x20;                        ▼

&#x20;                 Azure Logic Apps

&#x20;                        │

&#x20;                 Correlation ID

&#x20;                        │

&#x20;                        ▼

&#x20;                   ServiceNow

&#x20;                   │       │

&#x20;                Existing   New

&#x20;                   │       │

&#x20;                   ▼       ▼

&#x20;                 Update   Create



Objectives



\- Ingest and validate Microsoft Entra AuditLogs and SigninLogs

\- Develop KQL-based cloud identity detections

\- Detect suspicious OAuth administrative consent activity

\- Analyze unusual cloud sign-in context

\- Investigate generated Sentinel incidents

\- Automate incident handoff from Sentinel to ServiceNow

\- Implement correlation-based incident deduplication

\- Validate both ServiceNow create and update paths

\- Preserve existing ServiceNow work notes during incident updates



Tools and Technologies

Area	                  Technology

Cloud	               Microsoft Azure

Identity	      Microsoft Entra ID

SIEM	              Microsoft Sentinel

Log Management	      Azure Log Analytics

Query / Detection       	KQL

Automation	       Azure Logic Apps

ITSM	                  ServiceNow

API	               ServiceNow REST API



Detection 1 — OAuth Administrative Consent



The first detection focuses on successful tenant-wide OAuth administrative

consent activity.

I used the Entra AuditLogs table and filtered for application management

events where the operation was Consent to application.



The query also extracts consent-related fields such as:

\- Application name

\- Administrative consent

\- App-only context

\- Tenant-wide consent

\- Requested permissions



The detection generated a Sentinel incident from a controlled test OAuth

application.

The event was then investigated and classified as expected controlled lab

activity rather than being treated as proof of compromise.

The KQL used for this detection is available in:

detections/oauth-admin-consent.kql



Detection 2 — Unusual Cloud Sign-In Context



The second detection looks at a user's recent sign-in activity and compares

the recent sign-in city with the cities observed for that user during a

previous time window.



The final version uses:

\- 10-minute recent window

\- 24-hour baseline

\- User-based correlation

\- Sign-in city comparison



The detection was implemented and tuned to reduce repeated results.

The SigninLogs telemetry was successfully validated, but a positive alert was

not reproduced during the final validation run. I have therefore kept this

result documented as telemetry-positive and alert-inconclusive.

The KQL used for this detection is available in:

detections/unusual-signin-context.kql



Sentinel to ServiceNow Automation



I connected the Sentinel incident workflow to ServiceNow using an Azure Logic

App.

The Logic App receives the Sentinel incident and uses the Sentinel incident

number as the correlation value.

Before creating a new ServiceNow incident, it checks whether an incident with

the same correlation value already exists.



Sentinel Incident

&#x20;      │

&#x20;      ▼

Azure Logic App

&#x20;      │

&#x20;      ▼

ServiceNow List Records

&#x20;      │

&#x20;      ▼

Check correlation ID

&#x20;      │

&#x20;  ┌───┴────┐

&#x20;  │        │

&#x20;Found    Not Found

&#x20;  │        │

&#x20;  ▼        ▼

Update    Create

Incident  Incident



This prevents the same Sentinel incident from creating duplicate ServiceNow

records.



Validation



I tested the following parts of the workflow:

\- Azure and Sentinel environment

\- Entra AuditLogs ingestion

\- Entra SigninLogs ingestion

\- OAuth consent detection

\- Sentinel incident generation

\- ServiceNow API connectivity

\- ServiceNow incident creation

\- Correlation ID mapping

\- Existing incident update

\- Duplicate prevention

\- Work-note preservation

The create and update paths were both validated during testing.



Evidence



Selected screenshots are available in the evidence/ directory.



The evidence set focuses on the important implementation steps rather than

including every setup and troubleshooting screenshot.



Sensitive screenshots containing authentication or session-token material

have been excluded.



Project Documentation



The detailed implementation report is available in:

docs/SentinelOps-Technical-Report.pdf



Additional implementation notes are available in:

\- automation/

\- investigation/

\- detections/



Defender XDR



I evaluated Microsoft Defender XDR during the project, but it was not included

in the final operational workflow because the available tenant did not provide

the required licensing/access path.



Therefore, this repository does not claim hands-on Defender XDR implementation.



What I learned



This project gave me practical experience with:

\- Microsoft Sentinel

\- Microsoft Entra identity telemetry

\- KQL detection development

\- Cloud identity investigation

\- Sentinel incident handling

\- Azure Logic Apps

\- ServiceNow REST API integration

\- Incident correlation and deduplication

\- Basic SOC workflow automation

\- Security evidence handling

