&#x20;Sentinel to ServiceNow Automation



This document describes how I connected Microsoft Sentinel incidents to

ServiceNow using an Azure Logic App.



The goal was to automate the handoff of Sentinel incidents into ServiceNow

while avoiding duplicate tickets when the same Sentinel incident is processed

again.



&#x20;Workflow





Microsoft Sentinel

&#x20;       │

&#x20;       │ Sentinel incident trigger

&#x20;       ▼

Azure Logic App

&#x20;       │

&#x20;       ▼

ServiceNow - List Records

&#x20;       │

&#x20;       │ Search using correlation\_id

&#x20;       ▼

Check whether a matching incident exists

&#x20;       │

&#x20;      ┌┴───────────────┐

&#x20;      │                │

&#x20;    Found           Not Found

&#x20;      │                │

&#x20;      ▼                ▼

Update existing      Create new

ServiceNow incident  ServiceNow incident



Sentinel Incident Trigger



The workflow starts when a Sentinel incident triggers the Logic App.

The incident information is passed into the workflow so that the relevant

incident details can be used when creating or updating the ServiceNow record.



Correlation ID



I used the Sentinel incident number as the correlation value between Sentinel

and ServiceNow.



This gives the workflow a stable value that can be used to determine whether

a ServiceNow incident already exists for the Sentinel incident.



The basic relationship is:



Sentinel Incident Number

&#x20;         │

&#x20;         ▼

ServiceNow correlation\_id



ServiceNow Lookup



Before creating a new ServiceNow incident, the Logic App uses the ServiceNow

List Records operation to search for an existing record with the same

correlation ID.



The logic is:



ServiceNow List Records

&#x20;       │

&#x20;       ▼

correlation\_id = Sentinel incident number

&#x20;       │

&#x20;       ▼

Check result length



If a matching record is returned, the workflow follows the update path.



If no matching record is returned, the workflow follows the create path.



Create Path



When no ServiceNow incident contains the Sentinel incident's correlation ID,

the Logic App creates a new ServiceNow incident.



No matching record

&#x20;       │

&#x20;       ▼

Create ServiceNow incident

&#x20;       │

&#x20;       ▼

Store Sentinel correlation ID

This was tested successfully during the project.



Update Path



When a ServiceNow incident with the same correlation ID already exists, the

Logic App updates the existing incident instead of creating another record.



Matching record found

&#x20;       │

&#x20;       ▼

Update existing ServiceNow incident



This allows repeated processing of the same Sentinel incident to update the

existing ServiceNow record.



Duplicate Prevention

The create/update decision is based on the correlation ID.



Sentinel Incident

&#x20;      │

&#x20;      ▼

ServiceNow List Records

&#x20;      │

&#x20;      ▼

Same correlation\_id?

&#x20;      │

&#x20;  ┌───┴────┐

&#x20;  │        │

&#x20; Yes       No

&#x20;  │        │

&#x20;  ▼        ▼

&#x20;Update    Create



This prevents the same Sentinel incident from creating duplicate ServiceNow

records.



Work-Note Preservation



I also tested the update path against an existing ServiceNow incident.

The existing incident contained work notes before the update.



After the Logic App updated the incident, the existing work notes were still

present.

This was important because the automation should update the incident without

removing investigation information that was already recorded by an analyst.



Validation



The following parts of the automation were validated:

\- ServiceNow API connectivity

\- ServiceNow incident creation

\- Sentinel incident number mapping

\- Correlation ID mapping

\- ServiceNow List Records lookup

\- Create path

\- Update path

\- Duplicate prevention

\- Work-note preservation

Both the create and update paths were successfully tested.



Implementation Notes



The automation was built as a student lab using Azure Logic Apps and a

ServiceNow developer environment.

No production account-disable or application-revocation action was automated.

Authentication details, tokens, secrets, and other sensitive values are not

included in this repository.

Evidence

The main evidence for this workflow is maintained in the project's evidence/

directory.

The evidence demonstrates:

\- Sentinel incident triggering the workflow

\- ServiceNow incident creation

\- Correlation ID mapping

\- Existing incident update

\- Work-note preservation

\- Create/update deduplication

