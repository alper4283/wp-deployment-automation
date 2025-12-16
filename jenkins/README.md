# Jenkins Pipelines

This directory documents the Jenkins pipelines used in the WordPress deployment automation project.
The pipelines handle theme deployment, automated testing, and backup operations.

All pipelines are defined as Jenkinsfiles and are intended to be created as Jenkins Pipeline jobs.

---

## Pipeline: deploy-argon-theme

**Purpose**  
Implements a test → production deployment flow for a WordPress theme.

**Trigger**
- GitHub webhook (`githubPush()`)

**Flow**
1. Pulls the theme repository
2. Deploys the theme to the TEST WordPress VM
3. Activates the theme on TEST using WP-CLI
4. Runs Playwright + CucumberJS end-to-end tests against TEST
5. If tests pass:
   - Deploys the theme to the PROD WordPress VM
   - Activates the theme on PROD

**Parameters**
- `TEST_VM_IP` – IP/host of the test WordPress VM
- `PROD_VM_IP` – IP/host of the production WordPress VM
- `THEME_NAME` – WordPress theme directory name

**Required Jenkins credentials**
- `gcp-ssh-key`  
  Type: SSH Username with private key  
  Used to SSH and rsync files to both TEST and PROD VMs

**Requirements**
- Jenkins agent:
  - git, rsync, ssh
  - node + npm
  - ability to install Playwright (chromium)
- WordPress VMs:
  - WordPress located at `/var/www/html`
  - Passwordless sudo for rsync and WP-CLI installation
  - PHP and curl installed

---

## Pipeline: wp-backup-local

**Purpose**  
Creates automated daily backups of a WordPress instance on the target VM.

**Trigger**
- Cron: daily around 03:00

**Flow**
1. Connects to the target WordPress VM
2. Exports the database using WP-CLI
3. Creates a compressed archive of WordPress files
4. Stores backups locally on the same VM
5. Applies retention (keeps last 7 backups)

**Parameters**
- `VM_IP` – Target WordPress VM IP/host
- `SITE_TAG` – Label used in backup directory name (e.g. test / prod)

**Backup location on VM**
- `/var/backups/wp/<SITE_TAG>/`

**Required Jenkins credentials**
- `gcp-ssh-key`  
  Type: SSH Username with private key

**Requirements**
- Target VM:
  - WP-CLI available (installed automatically if missing)
  - Passwordless sudo
  - Sufficient disk space under `/var/backups/wp`

---

## Pipeline: wp-backup-offsite

**Purpose**  
Copies the latest TEST and PROD backups to a separate offsite backup VM.

**Trigger**
- Cron: weekly on Sunday around 04:00

**Flow**
1. Locates the most recent DB and site backups on TEST VM
2. Locates the most recent DB and site backups on PROD VM
3. Transfers backups via Jenkins workspace
4. Stores backups on a dedicated BACKUP VM

**Backup destination**
- `/srv/wp-offsite-backups/test`
- `/srv/wp-offsite-backups/prod`

**Required Jenkins credentials**
- `gcp-ssh-key`  
  Type: SSH Username with private key  
  Must be valid for:
  - TEST VM
  - PROD VM
  - BACKUP VM

**Requirements**
- Jenkins agent:
  - ssh, scp
  - enough workspace disk space for temporary files
- Backup VM:
  - Passwordless sudo
  - Sufficient disk space

**Note**
For simplicity in this demo setup, directory permissions are relaxed.
In a production system, permissions should be tightened.

---

## Manual / one-time setup steps

- Configure Jenkins SSH credentials (`gcp-ssh-key`)
- Configure GitHub webhook for the theme repository
- Ensure VM firewall rules allow SSH access from Jenkins

---

## Project context
These pipelines are part of a larger WordPress automation project that includes:
- Infrastructure provisioning (Pulumi)
- Configuration management (Ansible)
- Monitoring and logging
- Performance testing and evaluation
