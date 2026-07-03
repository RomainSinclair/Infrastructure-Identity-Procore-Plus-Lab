# Infrastructure Identity — Procore-Plus Lab

Infrastructure Provisioning & Identity Management — vSphere VM provisioning and resource changes, FreeIPA user management, SSH key distribution, GitLab repositories, and package repository troubleshooting.

## Environment
CentOS Stream / RHEL-based VMs in the Procore-Plus lab (dev-app, stage-web, dev-performance hosts), managed as a production-style environment with Jira ticket tracking.

## Skills & Tools
VMware vSphere (templates, snapshots, RAM changes), FreeIPA/Kerberos, SSH key generation & distribution, GitLab, dnf/yum repository management

## Tickets

| Ticket | Title | Documentation |
| --- | --- | --- |
| #37 | Users Reporting Repository Issues (dnf/yum) | [ticket-37-dnf-repo-issues.md](tickets/ticket-37-dnf-repo-issues.md) |
| #44 | FreeIPA: Export User List & Delete User | [ticket-44-freeipa-user-management.md](tickets/ticket-44-freeipa-user-management.md) |
| #55 | RAM Upgrade Request — dev-app (vSphere) | [ticket-55-ram-upgrade-devapp.md](tickets/ticket-55-ram-upgrade-devapp.md) |
| #58 | Create Your Own GitLab Repository | [ticket-58-gitlab-repository.md](tickets/ticket-58-gitlab-repository.md) |
| #12 | Deploy Dev Webserver Using vSphere Template | [ticket-tl12-deploy-dev-webserver.md](tickets/ticket-12-deploy-dev-webserver.md) |
| #15 | Create and Copy SSH Keys to Ansible & GitLab | [ticket-tl15-ssh-key-distribution.md](tickets/ticket-15-ssh-key-distribution.md) |

## About
Each ticket document includes the objective, environment details, step-by-step resolution, commands used, and verification/outcome. These reflect real hands-on system administration work completed in a lab modeled on production operations.
