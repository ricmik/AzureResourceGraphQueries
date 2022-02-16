# Log Analytics

## List of Log Analytics Workspaces with connected solutions

```kusto
resources
| where type == "microsoft.operationalinsights/workspaces"
| extend 
    skuName = properties.sku.name,
    workspaceRetention = properties.retentionInDays,
    workspaceid = tolower(tostring(id))
| join kind = leftouter (
    resources
    | where type == "microsoft.operationsmanagement/solutions" 
    | extend workspaceid = tolower(tostring(properties.workspaceResourceId)) 
    | extend solutionname = replace('\\(.*\\)|\\[.*\\]', '', name)
    | sort by solutionname asc 
    | summarize solutions = make_list(solutionname) by workspaceid
) on workspaceid
| project subscriptionId, name, resourceGroup, location, solutions, skuName, workspaceRetention
```