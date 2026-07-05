
| **Field** | Value |
| --- | --- |
| **Ticket #** | 58 |
| **Title** | Create Your Own GitLab Repository |
| **Category** | Infrastructure / Git Version Control |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

## Objective

Create a public GitLab repository named ansible-playbook-rs1 and upload Ansible playbooks and Bash scripts from dev-ansible. Provide the public repository URL as the deliverable.

## Requirements

- Public GitLab repository

- Repository contains Ansible playbooks and Bash scripts

- Provide the repository URL upon completion

- Repository name used: ansible-playbook-rs1

## Environment Details

- Tool: Git / GitLab

- Repo URL: https://gitlab.com/Romain_uno/ansible-playbook-rs1.git

- Host: dev-ansible

## Implementation Steps

Step 1 — Clean any previous clone attempt:

```bash
rm -rf ansible-playbook-rs1
```
Step 2 — Clone the new repo into the home directory:

```bash
git clone https://gitlab.com/Romain_uno/ansible-playbook-rs1.git
cd ansible-playbook-rs1
```
Step 3 — Create ansible and bash subdirectories:

```bash
mkdir -p ansible bash
```
Step 4 — Find playbooks and scripts on the system:

find /home/rsinclair -type f \( -name "*.yml" -o -name "*.sh" \)

Step 5 — Copy files into the repo:

```bash
cp /home/rsinclair/create_user_and_install_tmux.yml ansible/
cp /home/rsinclair/close_http_https_ports.yml ansible/
```
Step 6 — Stage, commit, and push:

```bash
git add .
git commit -m "Ticket 58: Add Ansible playbooks and Bash scripts"
git push origin main
```
## Troubleshooting & Root Cause Analysis

Multiple Git issues were encountered during this work: wrong file paths (files located on different hosts), cloning into a root-owned directory (causing permission denied on .git/FETCH_HEAD), GitLab authentication and personal access token issues, 'destination path already exists' error, and dubious ownership warnings from Git.

Resolution: start fresh with rm -rf ansible-playbook-rs1, clone into the user's home directory as the user (not root), locate actual playbook files with find, fix ownership with chown -R rsinclair:rsinclair ansible-playbook-rs1 if needed, and configure the GitLab token as the credential.

Git's dubious ownership warning occurs when the repo directory is owned by a different user than the one running git. Always clone and work in the same user context.

## Validation & Testing

```bash
git status
git remote -v
git push origin main
```
- Browse to https://gitlab.com/Romain_uno/ansible-playbook-rs1 to confirm files are visible

## Key Lessons Learned

- Always clone into the user's home directory, not root-owned paths — avoids dubious ownership errors

- Use find to locate actual files before copying — they may not be where you expect

- GitLab personal access tokens must be configured before git push will work


