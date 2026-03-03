# Bootstrap-AAP-POC

## This repo only supports:
- POC deployments for containerized AAP 2.6 [AAP growth topology](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/tested_deployment_models/container-topologies#cont-a-env-a) (this minimizes host and resource requirements).  
- Only bundeled installs for simplicity as this will work in disconnected environments as well as environments with internet access (the installer doesn't need to reach out)

>[!IMPORTANT]
>For disconnected installations, follow the steps in [Obtaining and configuring RHEL RPM source dependencies](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/containerized_installation/aap-containerized-disconnected-installation#obtaining-and-configuring-rpm-dependencies) (BaseOS and AppStream repos). 

## You Do
1. Provision a RHEL 9 or RHEL 10 server that meets these [Table 2.1. Virtual machine requirements](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/tested_deployment_models/container-topologies#infrastructure_topology) (may need to scroll past the diagram to see the table)
2. Configure the following on the Red Hat Enterprise Linux host
    - Configure a dedicated non-root user on the Red Hat Enterprise Linux host
    - Register your Red Hat Enterprise Linux host with `subscription-manager`
    - Install ansible-core
2. Sign up for [AAP Trial Subscription](https://www.redhat.com/en/technologies/management/ansible/trial?sc_cid=RHCTN0230000276044&gclsrc=aw.ds&&gclsrc=aw.ds&gad_source=1&gad_campaignid=20264085056&gbraid=0AAAAADsbVMSTHEHwUfwrKOd8tw57ZRPpn&gclid=EAIaIQobChMI_4uXuLyEkwMVpm5_AB1HtBDlEAAYASABEgJfdvD_BwE) (100 nodes for 60 days)
3. Clone this repo to your RHEL9 or RHEL10 Server while signed in as the non-root user (Where the [AAP growth topology](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/html/tested_deployment_models/container-topologies#cont-a-env-a) will be deployed)
4. Download the **Ansible Automation Platform 2.6 Containerized Setup Bundle** for  RHEL9 **OR** RHEL10 Installer to `files/` folder in this project
    - [**AAP 2.6 Containerized Setup Bundle - RHEL10**](https://access.redhat.com/downloads/content/480/ver=2.6/rhel---10/2.6/x86_64/product-software) 
    - [**AAP 2.6 Containerized Setup Bundle - RHEL9**](https://access.redhat.com/downloads/content/480/ver=2.6/rhel---10/2.6/x86_64/product-software)

4. Provide passwords
4. Run the playbook

## Playbook Does
1. Extract installer on your RHEL9 or RHEL10 AAP Host
2. Ensure host is configured as outlined by Preparing RHEL Host docs:
    - Ensure that the hostname of your host uses a fully qualified domain name (FQDN provided in `secrets.yml` file)
    - Verify that only the BaseOS and AppStream repositories are enabled on the host
    - 
2. Vault Encrypt secrets file
3. Copy/replace inventory/Inventory file to reflect bundled growth topology (essentially swap out fqdn for being deployed on)
3. Copy CasC file into installation directory (this will install the setup job templates and resources used in demo)