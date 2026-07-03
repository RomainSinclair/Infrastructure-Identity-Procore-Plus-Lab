# Ticket #55 — RAM Upgrade Request — dev-app

*Category: VMware vSphere / VM Resource Management*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 55 |
| **Title** | RAM Upgrade Request — dev-app |
| **Category** | VMware vSphere / VM Resource Management |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

## Objective

Increase RAM on dev-app by 500 MB in the virtualization platform. A clone of dev-app must be created before making any changes to protect the current state.

## Requirements

- VM: dev-app

- RAM increase: +500 MB

- Create a clone of dev-app before making changes

## Environment Details

- VM: dev-app

- Platform: VMware vSphere (ESXi 7.0)

- Action: Clone VM, then increase memory

## Implementation Steps

Step 1 — In vSphere Client, right-click dev-app-rs1 and select 'Clone to Virtual Machine'

Step 2 — Name the clone (e.g. dev-app-rs1-bak) and complete the clone wizard

Step 3 — Once cloned, power off dev-app-rs1 (RAM changes require VM to be off)

Step 4 — Edit settings: increase Memory to current + 500 MB

Step 5 — Power on dev-app-rs1 and confirm inside the OS:

```bash
free -m
```
## Troubleshooting & Root Cause Analysis

This was primarily a virtualization administration task. RAM changes in vSphere require the VM to be powered off unless memory hot-add is enabled on the VM hardware.

The key risk was ensuring the clone was fully completed and confirmed before modifying the production-like lab resource. Always verify the clone appears in the vSphere inventory before proceeding.

## Validation & Testing

```bash
free -m
```
- Expected: Memory total reflects the increased RAM value

## Key Lessons Learned

- Always clone before modifying VM hardware — provides instant rollback option

- RAM hot-add requires 'Memory Hot Add' to be enabled in VM settings (Edit Settings > Advanced)

- free -m shows memory in megabytes — verify the total matches the new allocated amount

## Screenshots

***Figure 1: vSphere VM summary — dev-performance-rs1 showing current memory (1 GB) before upgrade***

***Figure 2: vSphere clone wizard — storage selection step showing DS-01 datastore***
