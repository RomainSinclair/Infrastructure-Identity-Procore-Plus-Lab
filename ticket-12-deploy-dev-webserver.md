> **PROCORE-PLUS LAB** **Ticket TL-12: Deploy Dev Webserver Using vSphere Template** *VM Provisioning · Static IP · IPA Client · Asset Inventory*

| **Ticket ID**   TL-12 | **Reporter**   Procore Plus |
| --- | --- |
| **Project**   PROCORE-Plus Lab | **Assignee**   Romain Sinclair |
| **Type**   Task | **VM Target**   dev-performance-tl.procore.prod1 |
| **Priority**   Medium | **Date Resolved**   Mar 2026 |
| **Status**   Done ✓ | **Environment**   vSphere / CentOS 8 |

## 1. Objective

The web development team requires a new development web server deployed from the NEW-YT-DEV-WEBSERVER-TEMPLATE in vSphere. After deployment, the server must be configured with the correct hostname, static network connection, IPA client, and registered in the asset inventory.

> **📋  Task Requirements** VM Name: dev-performance-tl.procore.prod1 Network: Static IP connection (reference IPAM sheet for IP address) Package: ipa-client installed and joined to FreeIPA domain Asset Inventory: VM registered in Asset Tiger with serial number

## 2. Why vSphere Templates? The Engineer's Rationale

Rather than building a VM from scratch each time, vSphere templates provide a standardized, pre-configured OS image that enforces consistency across the infrastructure.

| **Concept** | **Explanation** |
| --- | --- |
| Standardization | Every VM from the same template starts identical — same OS, same base packages, same security posture. |
| Speed | Deploying from template takes minutes vs. hours for a manual install. Critical for scaling infra quickly. |
| Compliance | A known-good template ensures all VMs meet baseline security and configuration requirements from day one. |
| Rollback | If a deployment goes wrong, you simply delete and redeploy from the template — no manual cleanup. |

## 3. Infrastructure Architecture

> **🏗️  Deployment Flow** vSphere Client (NEW-YT-DEV-WEBSERVER-TEMPLATE)        ↓  Clone via "New VM from This Template" dev-performance-tl.procore.prod1  [10.1.30.x]        ├── Network: dev-performancetl (static, nmcli)        ├── IPA Client → joined to PROCORE.DEV domain        └── Asset Tiger → serial number registered

> **🔑  Key Concept: Static vs DHCP Networking** DHCP: IP assigned automatically on boot — can change between reboots. Bad for servers. Static: IP manually assigned and persistent — required for servers so DNS/hostname always resolves. Tool: nmcli (NetworkManager CLI) used to create and activate the static connection profile. Note: The old "prod-web" connection was disabled (autoconnect=no) to ensure only the static connection is active.

## 4. Step-by-Step Execution

### Step 1 — Find and Deploy from vSphere Template

From the vSphere Client, search for the template to use as the base image for the new VM.

```bash
# In vSphere Client:
# 1. Search bar → type: NEW
# 2. Select: NEW-YT-DEV-WEBSERVER-TEMPLATE
# 3. Actions → New VM from This Template
# 4. VM Name: dev-performance-tl.procore.prod1
# 5. Configure with default settings → Finish
```

> **💡  Engineer's Note** The template is based on CentOS 8 (ESXi 6.7+, VM version 14). DNS name: dev-webserver-template, Host: 10.11.40.

> **📸  Screenshot 1: vSphere search showing NEW-YT-DEV-WEBSERVER-TEMPLATE and template summary panel**

### Step 2 — Boot New VM and Resolve Boot Error

Power on the new VM. A boot error was encountered where the system stopped during startup.

```bash
# After powering on — VM hangs at boot
# Troubleshooting attempt 1: rd.break (failed)
# Troubleshooting attempt 2: Ctrl+D → clears the error
# System reboots successfully after Ctrl+D
```

> **⚠️  Troubleshooting: Boot Error** Symptom: VM powered on but stopped booting — blank screen or dracut shell. Cause: A filesystem check or init process error during first boot from template. Fix: Ctrl+D sends EOF signal, clears the error state, and allows the boot process to continue. Result: System rebooted cleanly and login prompt appeared.

> **📸  Screenshot 2: VM console showing boot error and resolution via Ctrl+D**

### Step 3 — Configure Hostname

After logging in, change the hostname from the template default to the required VM name.

```bash
# Set the hostname permanently hostnamectl set-hostname dev-performance-tl.procore.prod1
# Apply hostname change to current shell session exec bash
# Verify hostname
# Expected: dev-performance-tl.procore.prod1
```

> **💡  Engineer's Note** hostnamectl makes the change persistent across reboots. Without exec bash, the prompt still shows the old hostname for the current session.

> **📸  Screenshot 3: Terminal showing hostnamectl set-hostname and verification**

### Step 4 — Create Static Network Connection

Create a new static IP connection using nmcli and disable the template's default DHCP connection.

```bash
# Create a new static connection profile nmcli con add type ethernet con-name "dev-performancetl" ifname ens192 \   ip4 <IPAM_IP>/24 gw4 <GATEWAY>
# Bring up the static connection nmcli con up "dev-performancetl"
# Disable the old prod-web template connection nmcli con mod "prod-web" connection.autoconnect no
# Verify only the static connection is active nmcli dev status
```

> **📸  Screenshot 4: nmcli dev status output confirming dev-performancetl is the only active connection**

### Step 5 — Install and Configure IPA Client

Join the VM to the PROCORE.DEV FreeIPA domain for centralized authentication.

```bash
# Install the IPA client package yum install -y ipa-client
# Run IPA client setup (follow prompts from Ticket 5) ipa-client-install --domain=PROCORE.DEV --server=<IPA_SERVER>
# Verify user exists in IPA id tlindsey ipa user-show tlindsey
# If Kerberos error appears — obtain ticket first kinit tlindsey
# enter password ipa user-show tlindsey
# retry
```

> **⚠️  Troubleshooting: Kerberos Credentials Error** Error: ipa: ERROR: did not receive Kerberos credentials. Cause: IPA commands require an active Kerberos ticket to authenticate. Fix: Run kinit <username> first to obtain a Kerberos ticket, then retry the IPA command. Result: ipa user-show returned full user profile successfully.

> **📸  Screenshot 5: IPA client installation and kinit + ipa user-show successful output**

### Step 6 — Register VM in Asset Tiger

Retrieve the VM serial number and register the machine in the asset inventory tool.

```bash
# Get the VMware serial number
sudo dmidecode -s system-serial-number
# Output: VMware-42 0b 9f 28 53 a4 ce-09 ec 5c 1f 32 0a 1e 4d
# → Log into Asset Tiger and add new asset with this serial number
```

> **📸  Screenshot 6: dmidecode serial number output and Asset Tiger registration confirmation**

## 5. Challenges & Resolutions

| **Challenge** | **Symptom** | **Resolution** |
| --- | --- | --- |
| Boot error on first power-on | VM stopped booting after template clone, blank/dracut shell | Pressed Ctrl+D to clear the error and allow boot to continue |
| Kerberos credentials missing | ipa user-show failed with Kerberos error | Ran kinit <username> to obtain ticket before retrying IPA commands |
| Static IP configuration | Template had DHCP connection (prod-web) that needed replacing | Created new nmcli static connection, disabled prod-web autoconnect |

## 6. Verification Matrix

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | VM cloned from NEW-YT-DEV-WEBSERVER-TEMPLATE | vSphere Client → VM visible in inventory | ✅  Done |
| 2 | Hostname set to dev-performance-tl.procore.prod1 | hostname command output | ✅  Done |
| 3 | Static IP connection active (dev-performancetl) | nmcli dev status — only static connection shown | ✅  Done |
| 4 | prod-web connection autoconnect disabled | nmcli con show prod-web │ grep autoconnect | ✅  Done |
| 5 | ipa-client installed and joined to PROCORE.DEV | ipa user-show <user> returns data | ✅  Done |
| 6 | VM serial number retrieved | dmidecode -s system-serial-number | ✅  Done |
| 7 | VM registered in Asset Tiger | Asset Tiger entry with VMware serial number | ✅  Done |

## 7. Command Quick Reference

```bash
# ── DEPLOY ─────────────────────────────────────────────────────────────
# vSphere: Actions → New VM from This Template → NEW-YT-DEV-WEBSERVER-TEMPLATE
# ── HOSTNAME ─────────────────────────────────────────────────────────── hostnamectl set-hostname dev-performance-tl.procore.prod1 && exec bash
# ── STATIC NETWORK ───────────────────────────────────────────────────── nmcli con add type ethernet con-name "dev-performancetl" ifname ens192 ip4 <IP>/24 nmcli con up "dev-performancetl" nmcli con mod "prod-web" connection.autoconnect no
# ── IPA CLIENT ────────────────────────────────────────────────────────── yum install -y ipa-client && ipa-client-install kinit <username>
# obtain Kerberos ticket if needed
# ── ASSET TAG ───────────────────────────────────────────────────────────
sudo dmidecode -s system-serial-number
```

> **Ticket TL-12 · Deploy Dev Webserver Using vSphere Template · Procore-Plus Lab***  │  Assignee: Romain Sinclair · PROCORE Infrastructure Team*
