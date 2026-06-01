# AAP POC Accelerator (AAP 2.6)

## What This Does

Bootstraps a fully configured **Containerized AAP 2.6 POC** on a single RHEL 9 or RHEL 10 host using the [growth topology](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/plan-ref_cont_a_env_a#cont-a-env-a___infrastructure_topology) (Controller + Hub + EDA + Gateway, all on one machine).

After running, you get a fully licensed AAP instance with these resources pre-loaded and ready to consume:

| Resource | Details |
|----------|---------|
| Organization | Ansible Product Demos (APD) |
| Project | [Ansible Product Demos](https://github.com/ansible/product-demos) (GitHub) |
| Inventories | Demo Inventory, AWS |
| Job Template | APD \| Single demo setup |
| Credential | AAP Credential (wired to your instance) |

>[!NOTE]
>Only the **bundled install** option is used — no internet access required by the AAP installer itself. Works in both connected and disconnected environments.

>[!IMPORTANT]
>For disconnected installations, follow the steps in [Obtaining and configuring RHEL RPM source dependencies](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/containerized_installation/aap-containerized-disconnected-installation#obtaining-and-configuring-rpm-dependencies) (BaseOS and AppStream repos).

---

## Quick Start

### Step 1 — Provision a RHEL 9 or RHEL 10 Host

The host must meet the [growth topology hardware requirements](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/plan-ref_cont_a_env_a#cont-a-env-a___infrastructure_topology):

- 4+ vCPUs
- 16 GB RAM (32 GB recommended)
- 60 GB disk

Configure a **dedicated non-root user** with passwordless sudo on this host (see [prerequisites](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/install-proc_preparing_the_managed_nodes_for_containerized_installation)).

### Step 2 — Get an AAP Trial Subscription and Download the Manifest

0.1 Install ansible-core, git, wget

```bash
sudo dnf install -y ansible-core git wget
```

0.2 Clone/install this project on the target AAP host and cd into the project directory

**Option A — clone with git:**
```bash
git clone https://github.com/mkbredem/Bootstrap-AAP-POC.git
cd Bootstrap-AAP-POC
```

**Option B — extract from a downloaded tar archive:**
```bash
tar -xzf aap-poc-accelerator.tar.gz
cd aap-poc-accelerator
```

1. Sign up for an [AAP 60-Day Trial](https://www.redhat.com/en/technologies/management/ansible/trial) (100 managed nodes, no credit card)
2. Create a [Subscription Allocation](https://access.redhat.com/management/subscription_allocations/new):
   - **Name:** anything meaningful
   - **Type:** Satellite 6.`<latest>`
3. Click **Create**, then go to the **Subscriptions** tab
4. Click **Add Subscriptions** and set **Entitlements** to `100` for the 60-day trial SKU
5. Click **Submit**, then **Export Manifest** (top right)
6. Save the resulting `manifest_<Name>_<Timestamp>.zip` to the `files/` directory in this project

### Step 3 — Download the AAP Setup Bundle

Download the **Ansible Automation Platform 2.6 Containerized Setup Bundle** and save it to the `files/` directory:

- [AAP 2.6 Containerized Setup Bundle — RHEL 9](https://access.redhat.com/downloads/content/480/ver=2.6/rhel---9/2.6/x86_64/product-software)
- [AAP 2.6 Containerized Setup Bundle — RHEL 10](https://access.redhat.com/downloads/content/480/ver=2.6/rhel---10/2.6/x86_64/product-software)

### Step 4 — Edit `secrets.yml`

```yaml
aap_user: admin
aap_password: <your-chosen-admin-password>
aap_fqdn: <fqdn-of-your-rhel-host>       # e.g. aap.example.com
```

If your host is **not yet registered** with Red Hat, add one of these auth blocks (leave blank if already subscribed):

```yaml
# Preferred: activation key
rhsm_org: <your-org-id>
rhsm_activation_key: <your-activation-key>

# Alternative: username + password
# rhsm_username: <your-redhat-username>
# rhsm_password: <your-redhat-password>
```

### Step 5 — Clone This Repo to the RHEL Host and Run

```bash
git clone https://github.com/mkbredem/Bootstrap-AAP-POC.git
cd Bootstrap-AAP-POC
ansible-playbook -i aap_precheck_inventory site.yml
```

That's it. The playbook will:

1. **Precheck** — validate the host meets all requirements
2. **Prep** — register RHSM (if needed), install prerequisites, set FQDN, extract the bundle, render inventory + CaC files, vault-encrypt `secrets.yml`
3. **Install** — run the AAP containerized installer end-to-end

When complete, access your instance at `https://<aap_fqdn>` with the credentials from `secrets.yml`.

---

## Running Playbooks Individually

If you prefer to run steps separately:

```bash
# Validate host readiness only
ansible-playbook -i aap_precheck_inventory aap_precheck.yml

# Prepare files only (skip installer)
ansible-playbook -i aap_precheck_inventory aap_install_prep.yml

# Skip precheck on subsequent runs
ansible-playbook -i aap_precheck_inventory site.yml --skip-tags precheck
```

>[!IMPORTANT]
>If anything fails during precheck, fix the issue and rerun before proceeding.

---

## What Gets Pre-Loaded (CaC)

The `files/cac/` directory contains [Configuration as Code](https://github.com/redhat-cop/controller_configuration) templates that are rendered and applied automatically by the AAP installer's `controller_postinstall` feature.

| File | Contents |
|------|---------|
| `controller_organizations.yaml.j2` | Ansible Product Demos (APD) org |
| `controller_credentials.yaml.j2` | AAP credential wired to your instance |
| `controller_inventories.yaml.j2` | Demo Inventory + AWS inventory |
| `controller_projects.yaml.j2` | Ansible Product Demos GitHub project |
| `controller_job_templates.yaml.j2` | APD \| Single demo setup template |
| `inventory_sources.yaml.j2` | AWS EC2 inventory source |

To add more pre-loaded resources, drop additional `.yaml.j2` files into `files/cac/` following the [infra.controller_configuration](https://github.com/redhat-cop/controller_configuration) variable naming conventions.

---

## File Layout

```
aap-poc-accelerator/
├── site.yml                    # Single entry-point — run this
├── aap_precheck.yml            # Host readiness checks
├── aap_install_prep.yml        # Prep + installer (3 plays)
├── aap_precheck_inventory      # Localhost inventory
├── secrets.yml                 # Your credentials (vault-encrypted after first run)
├── files/
│   ├── readme.txt              # Instructions for files to place here
│   ├── manifest_*.zip          # Your subscription manifest (you provide)
│   ├── ansible-automation-platform-containerized-setup-bundle-*.tar.gz  # AAP bundle (you provide)
│   └── cac/                    # CaC templates pre-loaded into Controller
│       ├── controller_organizations.yaml.j2
│       ├── controller_credentials.yaml.j2
│       ├── controller_inventories.yaml.j2
│       ├── controller_projects.yaml.j2
│       ├── controller_job_templates.yaml.j2
│       └── inventory_sources.yaml.j2
└── templates/
    ├── inventory-growth.j2     # AAP installer inventory template
    └── aap_precheck_report.j2  # Precheck report template
```
