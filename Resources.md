# Azure Resources

## Count of resources per region
```kusto
resources
| summarize count() by location
| order by count_
```