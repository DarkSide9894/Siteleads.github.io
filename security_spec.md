# Security Specification - SiteLeads LA

## Data Invariants
1. A lead must have a `userId` matching the authenticated user.
2. `createdAt` must be set to the server time during creation.
3. `updatedAt` must be set to the server time during updates.
4. Terminal states (like `contacted`) should prevent certain field changes (like `name` or `address`).
5. ID strings must be validated for size and character set.

## The Dirty Dozen Payloads
1. **Unauthorized Create**: An unauthenticated user tries to create a lead.
2. **Identity Spoofing**: User A tries to create a lead with User B's `userId`.
3. **Ghost Field Update**: User tries to add an unassigned field `isAdmin: true` during update.
4. **ID Poisoning**: User tries to use a 2MB string as a Lead document ID.
5. **Timestamp Manual Set**: User tries to set `createdAt` to a date in 1999.
6. **Self-Promotion**: User tries to update their own `role` field (not applicable here yet, but good to prevent).
7. **Cross-User Read**: User A tries to get a lead owned by User B.
8. **Cross-User List**: User A tries to list all leads, including those owned by User B.
9. **Terminal State Break**: User tries to change business name after the lead is marked `contacted`.
10. **Type Poisoning**: User tries to set `websiteFound` to a 100kb string instead of a boolean.
11. **Invisible Access**: User tries to fetch leads without using a collection-wide query filter (Query Enforcer).
12. **Recursive Cost-Attack**: Attempting multiple `get()` calls in a rule by manipulating paths.

## Test Strategy
I will generate `firestore.rules` that block these vectors using the 8 pillars of hardened rules.
