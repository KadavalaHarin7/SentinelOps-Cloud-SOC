&#x20;ServiceNow Correlation and Deduplication Design



This document describes the correlation logic I used to prevent duplicate

ServiceNow incidents when the same Microsoft Sentinel incident is processed

more than once.



&#x20;Correlation Value



The Sentinel incident number is used as the stable correlation value.



The value is stored in the ServiceNow incident's `correlation\_id` field.





Microsoft Sentinel

&#x20;       │

&#x20;       │ Incident Number

&#x20;       ▼

ServiceNow correlation\_id



Decision Logic



Before creating a ServiceNow incident, the Azure Logic App searches

ServiceNow for an existing incident with the same correlation value.



Sentinel Incident

&#x20;      │

&#x20;      ▼

ServiceNow List Records

&#x20;      │

&#x20;      ▼

Search for matching correlation\_id

&#x20;      │

&#x20;      ▼

Check result

&#x20;      │

&#x20;  ┌───┴────┐

&#x20;  │        │

&#x20;Found    Not Found

&#x20;  │        │

&#x20;  ▼        ▼

Update    Create

existing  new

incident  incident



The decision is based on whether the ServiceNow lookup returns an existing

record.



Existing Incident — Update Path



If a matching correlation\_id is found, the Logic App updates the existing

ServiceNow incident.



Matching correlation\_id found

&#x20;           │

&#x20;           ▼

&#x20;   Existing incident

&#x20;           │

&#x20;           ▼

&#x20;         Update



A new ServiceNow incident is not created in this path.



No Existing Incident — Create Path



If the ServiceNow lookup does not find a matching correlation\_id, the Logic

App creates a new ServiceNow incident.



No matching correlation\_id

&#x20;           │

&#x20;           ▼

&#x20;     Create incident

&#x20;           │

&#x20;           ▼

&#x20;  Store correlation value



Why Correlation Is Used



Using the Sentinel incident number as the correlation value gives the

workflow a consistent way to associate a ServiceNow incident with its

originating Sentinel incident.



This also allows repeated processing of the same Sentinel incident to follow

the update path instead of creating another ServiceNow record.



Validation



I tested both paths during the project.



Create Path



A Sentinel incident with no existing matching correlation value was processed.

Result:

\- ServiceNow incident was created

\- Sentinel incident number was mapped as the correlation value



Update Path



The same correlation value was processed when a matching ServiceNow incident

already existed.

Result:

\- Existing ServiceNow incident was updated

\- A second incident was not created



Work-Note Preservation



I also validated the update path using an existing ServiceNow incident that

already contained work notes.

After the update, the existing work notes remained available in the incident.

This confirmed that the update workflow did not remove the existing incident

history during the tested update operation.



Result



The final workflow supports:

\- Sentinel incident correlation

\- ServiceNow record lookup

\- New incident creation when no match exists

\- Existing incident update when a match exists

\- Duplicate prevention

\- Preservation of existing work notes during the tested update path

