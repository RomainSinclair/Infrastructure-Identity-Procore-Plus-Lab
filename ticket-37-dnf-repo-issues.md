# Ticket #37 — Users Reporting Repository Issues

*Category: Infrastructure / Package Management*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 37 |
| **Title** | Users Reporting Repository Issues |
| **Category** | Infrastructure / Package Management |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

## Objective

Investigate and resolve why the del.extreme-ix repository was not functioning properly on dev-app. The baseurl was pointing to an invalid CentOS Stream 9 path, causing repomd.xml metadata failures.

## Requirements

- VM: dev-app

- Investigate the repository problem in /etc/yum.repos.d/del.extreme-ix.repo

- Restore the repository so package metadata can be downloaded successfully

- Hint: Reference Ticket #14 where the repository was originally created

## Environment Details

- VM: dev-app

- Service/Tool: dnf, yum repository configuration

- File: /etc/yum.repos.d/del.extreme-ix.repo

## Implementation Steps

Step 1 — List current repo files:

```bash
ls -l /etc/yum.repos.d/
```
Step 2 — Inspect the broken repo file:

```bash
cat /etc/yum.repos.d/del.extreme-ix.repo
```
Step 3 — Check if the repo shows up in dnf:

```bash
dnf repolist all | grep extreme
```
Step 4 — Test the configured baseurl with curl:

```bash
curl -I https://mirror.stream.centos.org/9-stream/Extras/x86_64/os/repodata/repomd.xml
```
Step 5 — Test alternate paths to find a working URL (CRB instead of Extras):

```bash
curl -I https://mirror.stream.centos.org/9-stream/CRB/x86_64/os/repodata/repomd.xml
```
Step 6 — Edit the repo file with the working CRB baseurl:

```bash
sudo vi /etc/yum.repos.d/del.extreme-ix.repo
```
Step 7 — Clear all DNF cache:

```bash
sudo dnf clean all
sudo rm -rf /var/cache/dnf
```
Step 8 — Rebuild the metadata cache for only this repo:

```bash
sudo dnf makecache --disablerepo="*" --enablerepo="del.extreme-ix"
```
Step 9 — Confirm the repo is now enabled:

```bash
dnf repolist | grep -i extreme
```
## Troubleshooting & Root Cause Analysis

The repository failed because the configured baseurl pointed to an invalid or deprecated CentOS Stream 9 path. The Extras repository path had changed and was returning HTTP 404 for repodata/repomd.xml.

Using curl -I to test the URL directly was the fastest way to confirm whether the path was valid — a 200 OK response confirms metadata is accessible; a 404 means the path is wrong.

After testing multiple URLs, the working path was identified as the CRB (CodeReady Builder) repository rather than Extras. The .repo file was updated accordingly.

After editing the repo file, the DNF cache must be fully cleared (dnf clean all plus manual rm -rf /var/cache/dnf) to force a fresh metadata download.

## Validation & Testing

```bash
curl -I https://mirror.stream.centos.org/9-stream/CRB/x86_64/os/repodata/repomd.xml
sudo dnf makecache --disablerepo="*" --enablerepo="del.extreme-ix"
dnf repolist | grep -i extreme
```
- Expected: HTTP/2 200 for repomd.xml, metadata cache created, repo appears in enabled list

## Key Lessons Learned

- A repo can exist on disk but still fail if the baseurl is invalid — always test the URL first

- curl -I is the fastest way to validate a repo metadata path before using dnf

- dnf clean all plus rm -rf /var/cache/dnf is required after changing .repo baseurl entries

- CentOS Stream 9 repo paths changed — Extras moved; CRB path is stable for this use case

## Screenshots

***Figure 1: dev-app — dnf repository configuration and repolist output***

***Figure 2: curl -I testing the repo URL — HTTP 200 confirms working CRB path***
