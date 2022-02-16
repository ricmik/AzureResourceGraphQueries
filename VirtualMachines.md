# Virtual Machines

## List of virtual machines with size and OS
```kusto
resources
| where type == "microsoft.compute/virtualmachines"
| extend 
    size = properties.hardwareProfile.vmSize,
    os = properties.extended.instanceView.osName
| sort by name asc 
| project name, location, size, os
```

## Count of operating system usage
```kusto
resources
| where type == "microsoft.compute/virtualmachines"
| extend os = iff(
    isnotempty(properties.extended.instanceView.osName),
    tostring(properties.extended.instanceView.osName),
    (iff
        (properties.extended.instanceView.powerState.code == "PowerState/running",
        strcat(properties.storageProfile.osDisk.osType, " unknown"),
        "deallocated")
        )
    )
| summarize count() by os
| order by count_ desc
```

## Count of VM size usage

```kusto
resources
| where type == "microsoft.compute/virtualmachines"
| project size = properties.hardwareProfile.vmSize
| summarize count() by tostring(size)
```