# Azure subscriptions

## List of Azure subscriptions with subscription type / quota ID
```kusto
resourcecontainers
| where type == "microsoft.resources/subscriptions"
| extend subscriptionType = tostring(properties.subscriptionPolicies.quotaId)
| project name, subscriptionId, subscriptionType
```