# Virtual Network

## List of subnets with connected Network Security Groups
```kusto
resources
| where type =~ 'microsoft.network/virtualnetworks'
| project subscriptionId, name, subnets = properties.subnets
| mv-expand subnets
| project subscriptionId, vnetname = name, subnetname = subnets.name, id = tolower(tostring(subnets.properties.networkSecurityGroup.id))
| join kind = leftouter (
    resources
    | where type =~ "microsoft.network/networksecuritygroups"
    | extend id = tolower(id)
    ) on id
| project subscriptionId, vnetname, subnetname, nsgname = name
```

## List of Network Security Groups
```kusto
resources
| where type == "microsoft.network/networksecuritygroups"
| sort by location asc 
| project name, location, resourceGroup
```

## List of Application Security Groups
```kusto
resources
| where type == "microsoft.network/applicationsecuritygroups"
| project name, location, resourceGroup
```

## List of ExpressRoute circuits
```kusto
resources
| where type == "microsoft.network/expressroutecircuits"
| project name, location, resourceGroup
```

## List of private endpoints with their connected services
```kusto
resources
| where type == "microsoft.network/privateendpoints"
| project name,location, connections = properties.privateLinkServiceConnections
| mv-expand connections
| project name,location, privateLinkServiceId = tolower(tostring(connections.properties.privateLinkServiceId))
| join kind = leftouter (
    resources
    | extend id = tolower(id)
) on $left.privateLinkServiceId == $right.id
| project name, location, ConnectedService = name1, ServiceType = type
```