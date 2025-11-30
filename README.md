Standard Load Balancer, IP SKUs & Backend Pools (Updated 2025)

This lab demonstrates how Azure Standard Load Balancers interact with different Public IP SKUs and how backend pool selection depends on SKU compatibility—not VM power state.


📘 Architecture Diagram

                     Internet
                         │
                         ▼
       ┌───────────────────────────────────┐
       │ Standard Public IP (pip-lb-front) │
       └───────────────────┬───────────────┘
                           │
                           ▼
                ┌──────────────────────────┐
                │  Standard Load Balancer   │
                │        (lb-standard)      │
                └─────────────┬────────────┘
                              │
             ┌────────────────┴────────────────┐
             ▼                                 ▼                      
    Allowed in Backend Pool           NOT Allowed in Backend Pool  
    (Standard SKU or No IP)            (Basic Public IP SKU)



---------------------------------------------------------------------------------------------
🎯 Goal of This Lab

Create a Standard Load Balancer

Create Basic and Standard Public IPs

Deploy 4 VMs with different IP SKUs

Attempt to add each VM to a backend pool

Understand why Basic SKU IPs cannot join a Standard LB

Observe that VM power state does not matter (Running vs Stopped)

🛠️ Environment Setup
VNet
Name	Address Space
vnet-lb-lab	10.10.0.0/16
Subnets
Subnet	Range
subnet1	10.10.1.0/24
subnet2	10.10.2.0/24
Virtual Machines
VM Name	Subnet	Public IP SKU	Power State	Backend Pool?
vm-lb1	subnet1	None	Running	✅ Allowed
vm-lb2	subnet2	Standard	Stopped (deallocated)	✅ Allowed
vm-lb3	subnet1	Standard	Running	✅ Allowed
vm-lb4	subnet2	Basic	Stopped (deallocated)	❌ Not allowed
⚠️ Important Azure Update (2025)
Basic Public IP and Basic Load Balancer have been officially retired by Microsoft (April 24, 2025).

This means:

You cannot create new Basic SKUs

Existing Basic SKUs may still appear on exams and legacy systems

Azure recommends upgrading to Standard SKUs for all new deployments

✔ Recommended Modern Replacement
Deprecated	Modern Alternative
Basic Public IP	Standard Public IP
Basic Load Balancer	Standard Load Balancer

This lab still teaches the SKU compatibility principle, which remains valid.

⚙️ Steps Performed

Created VNet + subnets

Deployed 4 VMs with mixed public IP SKUs

Stopped VM2 and VM4 (deallocated)

Created Standard Load Balancer (lb-standard)

Created Standard Public IP (pip-lb-front)

Attempted to add VMs to backend pool

Documented which ones were allowed vs blocked

Validated SKU rules + VM state impact

🧠 What You Should Understand
✔ VM power state does not matter

Stopped, deallocated, or running — all can join a backend pool.

✔ VMs without a Public IP can still join a Public LB backend

Backend pool membership is internal via NIC, not the Public IP.

✔ Public IP SKU must match the Load Balancer SKU

Standard LB ⟷ Standard Public IP
Basic IP ⟶ ❌ Cannot join Standard LB backend

✔ Basic SKU Public IP cannot be upgraded to Standard

You must create a new Standard IP.


This lab demonstrates the fundamental rules behind Azure load balancer backend membership:

Only Standard IPs can join Standard LBs

Basic SKUs are deprecated but still important for exam questions and legacy systems

VM state does NOT impact backend pool eligibility

SKU mismatch is the #1 reason VMs fail to join backend pools
