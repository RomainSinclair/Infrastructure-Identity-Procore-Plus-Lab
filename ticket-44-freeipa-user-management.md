# Ticket #44 — Get a List of All Users Enrolled on FreeIPA

*Category: FreeIPA / Identity Management*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 44 |
| **Title** | Get a List of All Users Enrolled on FreeIPA |
| **Category** | FreeIPA / Identity Management |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

## Objective

Generate a detailed list of all registered FreeIPA users and redirect the output to a file. This task builds mastery of centralized authentication and user/group management using FreeIPA.

## Requirements

- VM: dev-app

- Use IPA command output to list all enrolled users

- Redirect output to a file (/tmp/freeipa_users.txt)

- Output must include a detailed list of all registered FreeIPA users

- Kerberos authentication must be obtained first (kinit)

## Environment Details

- VM: dev-app

- Service/Tool: FreeIPA / Kerberos

- Output path: /tmp/freeipa_users.txt

## Implementation Steps

Step 1 — Escalate to root:

```bash
sudo -i
```
Step 2 — Obtain a Kerberos ticket:

```bash
kinit <freeipa_username>
```
Step 3 — Verify the Kerberos ticket:

klist

Step 4 — Export all FreeIPA users to file:

```bash
ipa user-find --all --raw > /tmp/freeipa_users.txt
```
Step 5 — Confirm the file was created:

```bash
ls -l /tmp/freeipa_users.txt
```
Step 6 — Count the lines to verify output size:

wc -l /tmp/freeipa_users.txt

Step 7 — Preview the output:

head -n 20 /tmp/freeipa_users.txt

## Troubleshooting & Root Cause Analysis

The primary blockers for this ticket were Kerberos authentication and IPA session state.

The workflow depends on obtaining a valid Kerberos ticket first using kinit. Without a valid TGT (Ticket Granting Ticket), the ipa command will refuse to run and return a Kerberos principal not found or credentials cache expired error.

Once authenticated, ipa user-find --all --raw provides the most complete user output, including raw LDAP attributes for each user. The --all flag includes all standard attributes; --raw returns the raw LDAP data rather than formatted output.

Output was redirected to /tmp/freeipa_users.txt for documentation and review.

## Validation & Testing

klist

```bash
ls -l /tmp/freeipa_users.txt
```
head -n 20 /tmp/freeipa_users.txt

- Expected: Kerberos ticket present, file exists with entries, preview shows user records

## Key Lessons Learned

- A valid Kerberos ticket (kinit) is required before any ipa command will work

- ipa user-find --all --raw returns LDAP-style output — more verbose but complete

- Redirecting with > /tmp/freeipa_users.txt stores the full result for auditing and ticketing

## Screenshots

***Figure 1: FreeIPA Active Users list — user psantana visible in the Identity dashboard***

***Figure 2: FreeIPA user profile for psantana showing account settings***

***Figure 3: Add user to User Groups dialog in FreeIPA***

***Figure 4: Group membership confirmation — psantana in ipausers and webmasters***
