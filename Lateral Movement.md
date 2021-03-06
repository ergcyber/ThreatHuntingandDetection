
# Lateral Movement

**for** referencing https://docs.microsoft.com/en-us/defender-for-identity/use-case-lateral-movement-path
// This query aims to visulize number of users and devices for the path of lateral movement towards High privileged accounts
// High numbers indicates potential risk for lateral movement associated with targeted accounts that security team can take actions to remediate as mentioned aboved MS documentation

```Kusto
IdentityDirectoryEvents 
| where ActionType == "Potential lateral movement path identified"
| extend ParsedFields = parse_json(AdditionalFields)
| extend NumberofRiskyUsers = array_length(ParsedFields.["FROM.ACCOUNT"])
| extend RiskyUsers = ParsedFields.["FROM.ACCOUNT"]
| extend NumberofRiskyDevices = array_length(ParsedFields.["FROM.DEVICE"])
| extend RiskyDevices = ParsedFields.["FROM.DEVICE"]
//| extend RiskySGGroups = ParsedFields.["FROM.GROUP"]
| extend NumberofRiskyEntityUsers = array_length(ParsedFields.["FROM.ENTITY_USER"])
//| where NumberofRiskyDevices > 25 or NumberofRiskyUsers > 25 // can be limited by numbers of users and devices to filter highest risk
| summarize make_set(RiskyDevices),make_set(RiskyUsers) by AccountName
| project AccountName, NRiskyUsers = array_length(set_RiskyUsers),NRiskDevices = array_length(set_RiskyDevices)
| render barchart 
```
***
