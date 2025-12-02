This lab guide is excellent, especially the inclusion of the 2025 retirement update and the clear breakdown of the SKU compatibility rules.

-----

# 🛡️ LAB 5 – Azure Standard Load Balancer, IP SKUs & Backend Pools

This lab demonstrates the critical compatibility rules governing the **Azure Standard Load Balancer**. Specifically, we explore how **Public IP SKUs** dictate which Virtual Machines (VMs) are eligible to join a Standard Load Balancer's backend pool, regardless of the VM's power state.

## 🎯 Lab Goals

  * Create an Azure **Standard Load Balancer**.
  * Deploy VMs with a mix of **Standard**, **Basic**, and **No Public IP** SKUs.
  * Attempt to add all VMs to the Standard Load Balancer's backend pool.
  * Validate that **Basic SKU Public IPs** cannot join a Standard Load Balancer.
  * Confirm that a VM's **power state** (Running vs. Stopped) does not affect eligibility.

-----

## 📘 Architecture Diagram

The Standard Load Balancer requires Standard SKU components for all front-end and back-end resources that utilize a Public IP.

```
                      Internet
                          │
                          ▼
        ┌───────────────────────────────────┐
        │ Standard Public IP (pip-lb-front) │
        └───────────────────┬───────────────┘
                            │
                            ▼
                  ┌──────────────────────────┐
                  │  Standard Load Balancer  │
                  │      (lb-standard)       │
                  └─────────────┬────────────┘
                              │
            ┌─────────────────┴─────────────────┐
            ▼                                   ▼
  ✅ Allowed in Backend Pool           ❌ NOT Allowed in Backend Pool
  (Standard SKU or No IP)               (Basic Public IP SKU)
```

-----

## ⚠️ Important Azure Update 

While this lab uses the Basic SKU to illustrate compatibility rules, note that the **Basic Public IP** and **Basic Load Balancer** SKUs were **retired on September 30, 2025** (originally April 24, 2025, but this is the latest in 2025).

  * **You cannot create new Basic SKUs** in the portal or using modern tools.
  * **Legacy systems and exam questions** may still reference Basic SKUs, making the compatibility rule critical knowledge.
  * **Recommendation:** Use **Standard SKU** for all new deployments.

| Deprecated | Modern Alternative |
| :--- | :--- |
| Basic Public IP | Standard Public IP |
| Basic Load Balancer | Standard Load Balancer |

-----

## 🛠️ Environment Setup

| Component | Name / Range |
| :--- | :--- |
| **VNet** | `vnet-lb-lab` (`10.10.0.0/16`) |
| **Subnet 1** | `subnet1` (`10.10.1.0/24`) |
| **Subnet 2** | `subnet2` (`10.10.2.0/24`) |
| **Load Balancer** | `lb-standard` (Standard SKU) |
| **Frontend IP** | `pip-lb-front` (Standard SKU) |

### Virtual Machines

| VM Name | Subnet | Public IP SKU | Power State | Backend Pool Status |
| :--- | :--- | :--- | :--- | :--- |
| **vm-lb1** | `subnet1` | **None** | Running | **✅ Allowed** |
| **vm-lb2** | `subnet2` | **Standard** | Stopped (Deallocated) | **✅ Allowed** |
| **vm-lb3** | `subnet1` | **Standard** | Running | **✅ Allowed** |
| **vm-lb4** | `subnet2` | **Basic** | Stopped (Deallocated) | **❌ Not allowed** |

-----

## ⚙️ Steps Performed

1.  Created VNet and subnets.
2.  Deployed four VMs (`vm-lb1` through `vm-lb4`) with the specified Public IP SKUs.
3.  Manually **stopped (deallocated)** `vm-lb2` and `vm-lb4`.
4.  Created the **Standard Load Balancer** (`lb-standard`) and its **Standard Public IP** (`pip-lb-front`).
5.  Attempted to add all four VMs (by NIC) to the Load Balancer's backend pool.
6.  Validated the resulting membership status.

## 🧠 Key Takeaways & Rules

This lab reinforces the fundamental rules for Azure Load Balancer backend membership:

1.  **SKU Compatibility is Mandatory:**

      * The **Standard Load Balancer** is fundamentally incompatible with the **Basic Public IP SKU**.
      * **Standard LB ⟷ Standard Public IP**. A Basic Public IP on a VM NIC will prevent that NIC from joining a Standard LB backend pool.
      * **Note:** A Basic SKU Public IP **cannot** be upgraded to Standard; you must create a new Standard IP resource.

2.  **VM Power State is Irrelevant:**

      * The VM's power state (**Running**, **Stopped**, or **Deallocated**) has **NO** impact on its eligibility for backend pool membership. The membership is determined solely by the properties of the associated **Network Interface Card (NIC)** and its attached resources (like the Public IP SKU).

3.  **VMs without Public IPs are Eligible:**

      * VMs that have **no Public IP** attached (`vm-lb1`) can successfully join a Public Load Balancer's backend pool. The Load Balancer directs traffic to the VM's private IP address via the NIC.

> **Conclusion:** **SKU mismatch** is the **\#1 reason** why VMs fail to join a Standard Load Balancer backend pool.

-----
