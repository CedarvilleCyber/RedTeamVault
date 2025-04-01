# Exploiting Dangerous Permissions
Some examples of dangerous permissions include:
- ForceChangePassword
	- User can forcibly change another users' password
- AddMembers
	- Users with this permission can add users, groups, and computers to another group
- GenericAll
	- Complete control over an object
- GenericWrite
	- Update any non-protected parameters of a target object
- WriteOwner
	- Update owner of a target object
- WriteDACL
	- Write new ACEs to the target object's DACL
- AllExtendedRights
	- Permission to perform any action associated with extended AD rights (including for changing password etc.)
- [[BloodHound]]
- [[PowerView]]
- Active Directory Module

```powershell
# Change an account password
$Password = ConvertTo-SecureString "<new pass>" -AsPlainText -Force
Set-ADAccountPassword -Identity "<AD user username>" -Reset -NewPassword $Password

# Add a user to a group
Add-ADGroupMember "IT Support" -Members "<AD user username>" 
```
