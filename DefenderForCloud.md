# Microsoft Defender for Cloud

## List of active defender plans per subscription
```kusto
resourcecontainers
| where type == "microsoft.resources/subscriptions"
| join kind = leftouter(
    securityresources
    | where type == "microsoft.security/pricings"
    | where properties.pricingTier != "Free"
    | sort by name asc
    | summarize defenderplans = make_list(name) by subscriptionId
) on subscriptionId
| extend numberofPlansEnabled = array_length(defenderplans)
| sort by name asc
| project name, numberofPlansEnabled, defenderplans
```

## List of active high severity low remediation effort recommendations
```kusto
securityresources
 | where type == "microsoft.security/assessments"
 | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
 | project
    ['DisplayName'] = properties.displayName,
    ['SubscriptionName'] = SubName,
    ['ResourceGroup'] = resourceGroup,
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['Description'] = properties.metadata.description,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['RemediationEffort'] = properties.metadata.implementationEffort,
    ['Severity'] = properties.metadata.severity,
    ['Categories'] = properties.metadata.categories,
    ['UserImpact'] = properties.metadata.userImpact,
    ['Threats'] = properties.metadata.threats,
    ['Link'] = properties.links.azurePortal
 | where Severity == "High" and RemediationEffort == "Low" and StatusCode == "Unhealthy"
 | order by tostring(DisplayName) asc , tostring(SubscriptionName) asc , tostring(ResourceGroup) asc , tostring(ResourceName) asc 
 ```