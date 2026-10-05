&#x20;OAuth Administrative Consent Investigation



This document records the investigation of the OAuth administrative consent

event detected by Microsoft Sentinel.



&#x20;Detection Trigger



The Sentinel analytics rule detected a successful OAuth administrative consent

operation in the Microsoft Entra `AuditLogs` table.



The event matched:



\- Category: `ApplicationManagement`

\- Operation: `Consent to application`

\- Result: `success`

\- Administrative consent: `True`

\- Tenant-wide consent: `True`



&#x20;Test Application



The activity was generated using the controlled lab application:



`Sentinel-OAuth-Lab-App`



The test application was used specifically to generate realistic

Microsoft Entra application-management telemetry for the detection.



The controlled test involved Microsoft Graph delegated `User.Read` permission

and tenant-wide administrative consent.



&#x20;Investigation



I reviewed the resulting Microsoft Sentinel incident and examined the

available consent-related fields from the Entra AuditLogs event.



The investigation focused on:



\- Application involved in the consent event

\- Consent context

\- Administrative consent status

\- Tenant-wide consent status

\- Requested permission scope

\- Whether the activity matched the controlled test



The event matched the expected activity generated during the lab test.



&#x20;Disposition



The incident was classified as:



"Expected controlled lab activity"



The OAuth consent event was not treated as evidence of compromise because the

activity was intentionally generated using the controlled test application.



This distinction is important because OAuth administrative consent can be

security-relevant, but the presence of a consent event by itself does not

establish that an account or application has been compromised.



&#x20;Investigation Outcome



The detection successfully identified the controlled OAuth administrative

consent activity and generated a Sentinel incident.



The incident was investigated and correctly classified as expected lab

activity.



This validated the detection and investigation workflow without making a

claim of real-world compromise.



&#x20;Evidence



The investigation evidence is maintained in the project's `evidence/`

directory.



Relevant evidence includes:



\- OAuth consent detection result

\- Sentinel incident generated from the detection

\- Incident investigation

\- Consent-related event details

