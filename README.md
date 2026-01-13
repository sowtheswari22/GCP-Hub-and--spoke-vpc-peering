# GCP-Hub-and--spoke-vpc-peering
Hub-and-Spoke using VPC Peering (GCP Console)
Target Design
Hub VPC  ⇄  Spoke-1 VPC
Hub VPC  ⇄  Spoke-2 VPC
Hub VPC  ⇄  Spoke-3 VPC

✔ Spokes → Hub communication allowed

❌ Spokes ↔ Spokes communication blocked

 STEP 1: Create Hub VPC

Open GCP Console

Select your project

Go to VPC network → VPC networks

Click CREATE VPC NETWORK

Hub VPC Configuration

Name: hub-vpc

Subnet creation mode: Custom

Create Subnet

Name: hub-subnet

Region: Your region

CIDR: 10.0.0.0/16

Click CREATE

 STEP 2: Create Spoke VPCs

Repeat the following steps for each spoke VPC.

Spoke-1

Name: spoke1-vpc

Subnet mode: Custom

Subnet name: spoke1-subnet

CIDR: 10.1.0.0/16

Spoke-2

Name: spoke2-vpc

CIDR: 10.2.0.0/16

Spoke-3

Name: spoke3-vpc

CIDR: 10.3.0.0/16

 CIDR ranges must NOT overlap

 STEP 3: Create VPC Peering (Hub → Spoke)
Hub ⇄ Spoke-1

Go to VPC network → VPC network peering

Click CREATE PEERING

Configuration

Name: hub-to-spoke1

Your VPC network: hub-vpc

Peered VPC network: spoke1-vpc

Enable

☑ Import custom routes

☑ Export custom routes

Click CREATE

 STEP 4: Create Reverse Peering (Spoke → Hub)

Stay in VPC network peering

Click CREATE PEERING

Configuration

Name: spoke1-to-hub

Your VPC network: spoke1-vpc

Peered VPC network: hub-vpc

Enable

☑ Import custom routes

☑ Export custom routes

Click CREATE

 STEP 5: Repeat Peering for Other Spokes

Repeat STEP 3 & STEP 4 for:

hub-vpc ⇄ spoke2-vpc
hub-vpc ⇄ spoke3-vpc

Final Peering Connections:

hub-vpc ⇄ spoke1-vpc
hub-vpc ⇄ spoke2-vpc
hub-vpc ⇄ spoke3-vpc
STEP 6: Configure Firewall Rules (VERY IMPORTANT)
 Allow Spoke → Hub Traffic

In hub-vpc:

Go to VPC network → Firewall

Click CREATE FIREWALL RULE

Rule Configuration

Name: allow-spokes-to-hub

Direction: Ingress

Source IP ranges:

10.1.0.0/16

10.2.0.0/16

10.3.0.0/16

Action: Allow

Protocols: ICMP / TCP / All (as required)

Click CREATE

 Block Spoke-to-Spoke Traffic

Create deny rules in each spoke VPC.

Example: spoke1-vpc

Name: deny-spoke1-to-other-spokes

Direction: Ingress

Source ranges:

10.2.0.0/16

10.3.0.0/16

Action: Deny

Priority: 500

Repeat similar rules for spoke2 and spoke3.

 STEP 7: Create Test VMs

Create VMs in:

hub-vpc

spoke1-vpc

spoke2-vpc

 Create a VM in GCP (Console – Step by Step)
 PART A: Create Hub VM

Go to Compute Engine → VM instances → CREATE INSTANCE

Name: hub-vm

Region: Same as hub subnet

Machine type: e2-micro

OS: Ubuntu 20.04 LTS

Disk size: 10 GB

Networking (Important)

Network: hub-vpc

Subnet: hub-subnet

External IP: None

Network Tags

hub-vm

Click CREATE

PART B: Create Spoke VM (Repeat for each spoke)

Name: spoke1-vm

Network: spoke1-vpc

Subnet: spoke1-subnet

External IP: None

Network tag: spoke-vm

Click CREATE

 PART C: Verify Firewall Rules

Go to VPC network → Firewall

Ensure rules show status:

✅ Active

 PART D: Test Connectivity
 Spoke → Hub (Should Work)

From spoke1-vm:

ping <hub-vm-internal-ip>
Spoke → Spoke (Should Fail)

From spoke1-vm:

ping <spoke2-vm-internal-ip>
Final Result

 Spoke → Hub connectivity works

 Spoke → Spoke connectivity is blocked

 Notes

This setup uses VPC Network Peering (non-transitive)

For spoke-to-spoke or centralized routing, use Network Connectivity Center (NCC) or Shared VPC
