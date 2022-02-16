# Azure Policy

## List of policy assignments excluding defender for cloud / security baseline policies

```kusto
policyresources
| where type == "microsoft.authorization/policyassignments"
| extend 
    displayName = properties.displayName,
    parameters = properties.parameters,
    scope = properties.scope,
    identity = identity.principalId
| where name !contains "SecurityCenter" and name !startswith "Defender" and name !startswith_cs "ASC" and displayName !contains "Benchmark"
| project  displayName, scope, parameters, identity
```