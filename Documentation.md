# PROJECT DOCUMENTATION
# Securing Azure VM Remote Access Using Azure Bastion and NSGs

## 1. Introduction

This project involved deploying and securing a Windows Virtual Machine in Microsoft Azure using Azure Bastion and Network Security Groups (NSGs).

The primary objective was to establish secure remote administrative access without relying on direct public internet exposure of the virtual machine's Remote Desktop Protocol (RDP) service.

The project provided practical experience with Azure infrastructure deployment, virtual networking, network security, connectivity troubleshooting, security hardening, and cloud resource management.

## 2. Problem Statement

Remote Desktop Protocol (RDP) uses TCP port 3389 by default.

Allowing unrestricted inbound RDP traffic from the internet creates a security risk because the virtual machine becomes exposed to potential unauthorized access attempts, brute-force attacks, and credential attacks.

The goal of this project was to:

- Deploy a Windows Virtual Machine in Azure.
- Establish remote access using Azure Bastion.
- Avoid relying on direct public RDP connectivity.
- Restrict inbound network traffic using NSG rules.
- Validate secure remote connectivity.
- Apply network security hardening.
- Clean up deployed resources to prevent unnecessary cloud costs.

## 3. Project Architecture

The deployment used the following architecture:

Resource Group
|
|-- Virtual Network: vnet-eastus-1
|   |
|   |-- VM Subnet: snet-eastus-1
|   |   |-- Windows Virtual Machine
|   |
|   |-- AzureBastionSubnet
|       |-- Azure Bastion
|       |-- Bastion Public IP
|
|-- Network Security Group
    |-- Inbound security rules

### Network Configuration

| Component | Configuration |
|---|---|
| Cloud provider | Microsoft Azure |
| Deployment region | East US |
| Virtual Network | vnet-eastus-1 |
| VM subnet | snet-eastus-1 |
| VM subnet address range | 172.16.0.0/24 |
| Bastion subnet | AzureBastionSubnet |
| Bastion address range | 172.16.1.0/26 |
| Bastion public IP | CloudSecurity-Bastion-PIP |
| Bastion resource | CloudSecurity-Bastion |
| Remote access protocol | RDP |
| RDP port | TCP 3389 |

**Important architectural decision:** Azure Bastion and the Windows VM used separate subnets within the same virtual network. The VM was not placed inside the dedicated AzureBastionSubnet.

## 4. Implementation Process

### Phase 1: Creating the Resource Group

A dedicated Azure Resource Group was created specifically for the project.

This provided a central location for managing the resources associated with the deployment.

Using a dedicated resource group also made it easier to manage and eventually delete the project infrastructure.

### Phase 2: Creating the Virtual Network

A virtual network named `vnet-eastus-1` was created in the East US region.

The virtual network provided the private network infrastructure required for communication between the Windows VM and Azure Bastion.

Two subnets were configured:

1. `snet-eastus-1` – Used by the Windows Virtual Machine.
2. `AzureBastionSubnet` – Dedicated to the Azure Bastion deployment.

The subnet configurations were:

- VM subnet: 172.16.0.0/24
- Azure Bastion subnet: 172.16.1.0/26

The dedicated Bastion subnet was important because Azure Bastion requires its own subnet with the exact name `AzureBastionSubnet`.

### Phase 3: Deploying the Windows Virtual Machine

A Windows Virtual Machine was provisioned inside the `snet-eastus-1` subnet.

The VM was configured to use a private IP address for communication within the virtual network.

The intention was to access the machine through Azure Bastion instead of exposing its RDP service directly to the internet.

### Phase 4: Deploying Azure Bastion

Azure Bastion was deployed into the dedicated `AzureBastionSubnet`.

The deployment included a public IP resource named:

`CloudSecurity-Bastion-PIP`

The Bastion resource was named:

`CloudSecurity-Bastion`

Azure Bastion provides browser-based remote connectivity to virtual machines through the Azure portal without requiring a public IP address on the target VM.

### Phase 5: Configuring Network Security Groups

A Network Security Group was configured to control inbound traffic to the virtual machine.

The initial RDP rule permitted traffic from Any source, which was broader than required.

This was subsequently restricted to the Azure Bastion subnet.

The final intended inbound RDP rule was:

| Setting | Value |
|---|---|
| Source | IP Addresses |
| Source IP | 172.16.1.0/26 |
| Source port | * |
| Destination | Any |
| Destination port | 3389 |
| Protocol | TCP |
| Action | Allow |
| Priority | 300 |
| Name | RDP from Azure Bastion |

This configuration permits RDP traffic originating from the Bastion subnet rather than allowing unrestricted inbound RDP traffic.

### Phase 6: Troubleshooting Remote Connectivity

During the implementation, the initial remote connection did not work as expected.

Several configuration areas were investigated, including:

- Virtual network selection.
- Subnet assignment.
- Azure Bastion deployment.
- NSG inbound security rules.
- RDP port configuration.
- Windows Firewall settings.

One important discovery was that selecting the correct virtual network automatically changed the available subnet selection to `AzureBastionSubnet` when configuring Bastion.

The VM and Bastion subnet assignments were subsequently checked to ensure that each resource was placed in its correct subnet.

The Windows Firewall profiles were also investigated because their default firewall configurations were blocking the relevant remote desktop traffic.

After the connectivity configuration was addressed, the Bastion remote desktop session was successfully established.

### Phase 7: Security Hardening

After confirming that remote access was working, the next step was to strengthen the network security configuration.

The existing NSG rules were reviewed.

The following inbound rules were identified:

| Priority | Rule | Protocol | Source | Action |
|---|---|---|---|---|
| 300 | RDP from Azure Bastion | TCP 3389 | 172.16.1.0/26 | Allow |
| 65000 | AllowVnetInBound | Any | VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInBound | Any | AzureLoadBalancer | Allow |
| 65500 | DenyAllInBound | Any | Any | Deny |

The RDP rule had priority 300, meaning it was evaluated before the default inbound rules.

The default rules were retained.

**Security consideration:** NSGs are evaluated using priority values, with lower numbers evaluated first. The explicit RDP rule therefore takes precedence over the default deny-all inbound rule.

The objective was to permit administrative access through Bastion while maintaining the default inbound network restrictions.

### Phase 8: Validating the Deployment

The deployment was tested by initiating a remote desktop connection through Azure Bastion.

The connection was successfully established.

This demonstrated that:

- The VM was reachable through the Bastion service.
- The VM and Bastion were connected through the intended virtual network.
- The NSG permitted the required RDP traffic from the Bastion subnet.
- Remote administrative access could be established without relying on direct public RDP access to the VM.

### Phase 9: Resource Cleanup and Cost Awareness

After completing the project, a decision was made to stop the lab to avoid unnecessary Azure costs.

A dedicated Resource Group had been created for the project, so the entire resource group was deleted.

After deletion, the Azure All Resources search showed NetworkWatcher as a remaining resource.

This highlighted an important cloud management lesson: deleting a project resource group does not automatically establish that every Azure-related service or separately managed resource has been removed.

Azure Bastion can incur charges while deployed, so resource deletion and billing verification are important parts of cloud lab management.

For future deployments, Azure Cost Management should be used to review actual usage and confirm that no project-related billable resources remain.

## 5. Security Controls Implemented

The following security controls were demonstrated:

1. Dedicated subnet allocation for Azure Bastion.
2. Separation of the VM subnet and Bastion subnet.
3. Restricted inbound RDP traffic to the Bastion subnet.
4. Explicit NSG rule prioritization.
5. Retention of default inbound deny rules.
6. Remote access validation through Azure Bastion.
7. Resource cleanup after completing the lab.

## 6. Challenges Encountered and Lessons Learned

| Challenge | Lesson learned |
|---|---|
| Initial Bastion connectivity problems | Verify VNet, subnet, NSG and firewall configurations systematically. |
| Incorrect or broad RDP source configuration | Restrict administrative traffic to trusted network ranges. |
| Bastion subnet selection | Azure Bastion requires a dedicated subnet named AzureBastionSubnet. |
| NSG priority configuration | Lower priority numbers are evaluated first. |
| Windows Firewall restrictions | Network security rules and host firewall rules must both permit the required traffic. |
| Concern about Azure billing after deletion | Always verify remaining resources and actual costs using Cost Management. |

## 7. Project Outcome

The project successfully demonstrated secure remote administration of an Azure Windows Virtual Machine using Azure Bastion.

The implementation reduced the need for direct public RDP exposure and applied network-level restrictions to the VM's RDP traffic.

It also provided practical experience in troubleshooting cloud connectivity, configuring NSGs, understanding Azure subnet architecture, and managing cloud resource costs.

## 8. Skills Demonstrated

- Microsoft Azure administration
- Azure Virtual Machine deployment
- Azure Bastion configuration
- Virtual Network and subnet management
- Network Security Group configuration
- RDP security
- Windows Firewall troubleshooting
- Cloud security hardening
- Cloud infrastructure lifecycle management
- Azure cost awareness

## 9. Conclusion

This project demonstrates how cloud infrastructure can be deployed with security considerations incorporated into the network architecture.

Rather than exposing a Windows VM directly to the internet for remote administration, Azure Bastion was implemented as the remote access solution, with NSG rules restricting RDP traffic to the Bastion subnet.

The experience reinforced the importance of secure network segmentation, least-privilege network access, systematic troubleshooting, and responsible cloud resource management.

---

**Project Type:** Cloud Security / Infrastructure Security

**Platform:** Microsoft Azure

**Environment:** Personal Azure Lab

**Status:** Completed and resources cleaned up.
