# Azure Advisor

## List of all high impact, non-security related recommendations from Azure Advisor
```kusto
advisorresources
| where type == "microsoft.advisor/recommendations"
| where properties.category != "Security" and properties.impact == "High"
| extend 
    recommendation = tostring(properties.shortDescription.solution),
    resourceId = tostring(properties.resourceMetadata.resourceId),
    impactedResource = tostring(properties.impactedValue),
    impactedResourceType = tostring(properties.impactedField),
    category = tostring(properties.category),
    impact = tostring(properties.impact)
| summarize count() by recommendation,category,impact
```