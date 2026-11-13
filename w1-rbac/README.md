# Week 1: RBAC lab

## What a role assignment is
A role assignment answers three questions:
- **Who:** the user, group, or identity getting access (here, the group `grp-readers`, which contains `test-reader`)
- **What role:** the set of permissions (Reader, then Contributor)
- **Which scope:** where the access applies (here, the resource group `rg-w1-rbac`)

I assigned roles to a group instead of a user, so access follows group
membership and is managed in one place.

## What happened at each test
1. **Reader on rg-w1-rbac:** `test-reader` could open and view the
resource group. Creating a storage account was denied
with an authorization error. 
2. **Contributor on rg-w1-rbac:** I added Contributor for the group
and retested. The create still failed, this time with a message that
the Microsoft.Storage resource provider was not registered and I did not
have permission to register it.
3. Went to subscription>resource providers>searched for microsoft.storage and registered it.After registering I was able to create the storage account under the rg-w1-rbac resource group 
4. **Cleanup:** I deleted the test storage account and removed the
Contributor assignment, so the group is back to Reader only.
`az role assignment list --resource-group rg-w1-rbac -o table`
confirmed it.
Principal    Role    Scope
-----------  ------  -----------------------------------------------------------------------------
grp-readers  Reader  /subscriptions/f9db55e8-2460-45dc-a41a-2cf8ee37cdef/resourceGroups/rg-w1-rbac

## What surprised me
Contributor on a resource group was not enough to create a storage account.
Registering a resource provider is a subscription-level action, so it needs
a role at the subscription scope. This showed me that scope matters as much
as the role: the same role can behave differently depending on where it is
assigned.

## Takeaways
- Give the least access needed, at the smallest scope needed.
- Assign roles to groups, not individuals.
- Role changes can take several minutes, so sign out and in before retesting.
- Read errors carefully: "not authorized" and "provider not registered"
are different problems with different
