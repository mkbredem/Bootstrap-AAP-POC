1) Place the AAP installation bundle tar file in this directory.

if you can pull files from the internet, download appropriate RHEL 9 or RHEL 10 containerized installation bundle from:

RHEL9: https://access.redhat.com/downloads/content/480/ver=2.6/rhel---9/2.6/x86_64/product-software
RHEL10: https://access.redhat.com/downloads/content/480/ver=2.6/rhel---10/2.6/x86_64/product-software

2) Download and save a subscription manifest to this files/ directory in this project 

   2.1 Create a new Subscription Allocation (follow link and populate fields below)
       - Name: <a_meaningful_name>
       - Type: Satellite 6.<latest>

   2.2 Click Create (button)

   2.3 Go to Subcriptions (tab) and click Add Subscriptions (button)

   2.4 Set Entitlements (field) to 100 for the "60 Day Product Trial of Red Hat Ansible Automation Platform, Self-Supported (100 Managed Nodes)" subscription

   2.5 Click Submit (button)
   
   2.6 Click Export Manifest (botton - top right) -> Saved workstation Downloads folder 2.2 Save the resulting ~/Downloads/manifest_<Name>_<TimeStamp>.zip file to the Bootstrap-aap-poc'/files/ folder