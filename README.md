# Connect to Azure AD
Connect-AzAccount

# Define variables
$createdDateTime = (Get-Date).AddDays(-3)  # Users created within last 3 days
$targetGroupName = "Allow MFA Reset and Register - New Users"
$groupName = "Russell Cellular-APP Users" #Get the target Active Directory group
$azureUsers = Get-AzADGroupMember -GroupDisplayName $groupName | Select-Object CreatedDateTime, DisplayName, Id, UserPrincipalName  | Where-Object {$_.createdDateTime -ge $createdDateTime} | Out-String
$user = (Get-AzADGroupMember -GroupDisplayName $targetGroupName).UserPrincipalName
$userId = (Get-AzADGroupMember -GroupDisplayName $groupname | Where-Object {$_.createdDateTime -ge $createdDateTime}).Id
$groupId = (Get-AzADGroup -DisplayName $targetGroupName).Id

# Add new users to the target group
foreach ($newUser in $azureUsers)
{
    Add-AzADGroupMember -TargetGroupObjectId $groupId -MemberObjectId $userId
    Write-Host $newUser
}
