> **PROCORE-PLUS LAB** **Ticket TL-15: Create and Copy SSH Keys to Ansible & GitLab** *SSH Keygen · Passwordless Auth · GitLab · FreeIPA User*

| **Ticket ID**   TL-15 | **Reporter**   Procore Plus |
| --- | --- |
| **Project**   PROCORE-Plus Lab | **Assignee**   Romain Sinclair |
| **Type**   Task | **Date**   March 2026 |
| **Priority**   Medium | **Source Servers**   dev-app, dev-performance |
| **Status**   In Progress | **Targets**   dev-ansible, GitLab |

## 1. Objective

The security and network team requires all users to generate SSH key pairs on their development servers and configure passwordless SSH authentication to the Ansible control node and GitLab repository. This enables secure, automated operations without password prompts.

> **📋  Task Requirements** Passwordless SSH: dev-app ↔ dev-ansible.procore.prod1 Passwordless SSH: dev-app → GitLab account (git@gitlab.com) Passwordless SSH: dev-performance ↔ dev-ansible.procore.prod1 Passwordless SSH: dev-performance → GitLab account IMPORTANT: Log in as your FreeIPA user when generating keys

## 2. Why SSH Keys? The Engineer's Rationale

| **Concept** | **Explanation** |
| --- | --- |
| Passwordless Auth | SSH keys eliminate password prompts — critical for Ansible which runs automated operations across many servers. |
| Security | Cryptographic key pairs are far more secure than passwords — immune to brute-force and credential stuffing attacks. |
| GitLab Access | SSH keys allow git operations (clone, push, pull) without entering GitLab credentials every time — essential for CI/CD. |
| FreeIPA Integration | Generating keys as the FreeIPA user ensures the key is associated with the correct identity for centralized auth management. |

## 3. Step-by-Step Execution

### Step 1 — Generate SSH Key Pair on dev-app

```bash
# Login to dev-app as your FreeIPA user ssh <freeipa-user>@dev-app.procore.prod1
# Generate the SSH key pair (press ENTER for all prompts) ssh-keygen
# Generating public/private rsa key pair.
# Enter file in which to save the key (/home/<user>/.ssh/id_rsa): [ENTER]
# Enter passphrase (empty for no passphrase): [ENTER]
# Enter same passphrase again: [ENTER]
# Verify keys were created ls -la ~/.ssh/
```

> **⚠️  Troubleshooting: Key Stored in File Named "yes"** Mistake: At the "Enter file" prompt, typed "yes" instead of pressing Enter. Result: Private key was saved to a file literally named "yes" — not the default ~/.ssh/id_rsa. Fix: Press ENTER for ALL prompts to accept defaults. Never type "yes" at the file location prompt. Lesson: The ssh-keygen prompts are "accept/decline" in plain language — read carefully before typing.

> **📸  Screenshot 1: ssh-keygen output on dev-app showing key pair generation**

### Step 2 — Copy SSH Key to dev-ansible

```bash
# Copy the public key to the Ansible control node ssh-copy-id <user>@dev-ansible.procore.prod1
# Enter FreeIPA password when prompted
# Verify passwordless login works ssh <user>@dev-ansible.procore.prod1
# Should connect WITHOUT asking for a password
```

> **📸  Screenshot 2: ssh-copy-id to dev-ansible and passwordless SSH login confirmation**

### Step 3 — Add Public Key to GitLab Profile

```bash
# Display your public key cat ~/.ssh/id_rsa.pub
# Copy the entire output (starts with ssh-rsa ...)
# → Login to GitLab web UI
# → User Settings → SSH Keys
# → Paste public key → Add Key
# Test GitLab SSH connection ssh -T git@gitlab.com
# Expected: Welcome to GitLab, @<username>!
```

> **📸  Screenshot 3: GitLab SSH key settings page and ssh -T git@gitlab.com welcome message**

### Step 4 — Repeat on dev-performance

```bash
# Login to dev-performance as FreeIPA user ssh <user>@dev-performance.procore.prod1
# Generate key pair (same process as dev-app) ssh-keygen
# Press ENTER for all prompts
# Copy to dev-ansible ssh-copy-id <user>@dev-ansible.procore.prod1
# Test connection ssh <user>@dev-ansible.procore.prod1
```

> **📸  Screenshot 4: dev-performance ssh-keygen and ssh-copy-id to dev-ansible**

## 4. Verification Matrix

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | SSH key pair generated on dev-app | ls -la ~/.ssh/ shows id_rsa and id_rsa.pub | ✅  Done |
| 2 | Public key copied to dev-ansible from dev-app | ssh-copy-id: "Number of key(s) added: 1" | ✅  Done |
| 3 | Passwordless SSH to dev-ansible from dev-app | ssh to dev-ansible connects without password | ✅  Done |
| 4 | Public key added to GitLab profile | GitLab Settings → SSH Keys shows key | ✅  Done |
| 5 | SSH -T git@gitlab.com returns welcome message | Welcome to GitLab, @<username>! | ✅  Done |
| 6 | SSH key pair generated on dev-performance | ls -la ~/.ssh/ shows id_rsa and id_rsa.pub | ✅  Done |
| 7 | Passwordless SSH to dev-ansible from dev-performance | ssh connects without password | ✅  Done |

> **Ticket TL-15 · Create and Copy SSH Keys to Ansible & GitLab · Procore-Plus Lab***  │  Assignee: Romain Sinclair · PROCORE Infrastructure Team*
