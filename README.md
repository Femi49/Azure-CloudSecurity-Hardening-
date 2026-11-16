# Securing Azure VM Remote Access Using Azure Bastion and NSGs

<!-- SHIELDS.IO BADGES -->
![Microsoft Azure](https://img.shields.io/badge/Microsoft%20Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Cloud Security](https://img.shields.io/badge/Cloud-Security-00A86B?style=for-the-badge&logo=shield&logoColor=white)
![Azure Bastion](https://img.shields.io/badge/Azure-Bastion-0078D4?style=for-the-badge)
![Network Security](https://img.shields.io/badge/Network-Security-512BD4?style=for-the-badge)
![Windows Server](https://img.shields.io/badge/Windows-Server-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Project Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

## Project Overview

This project demonstrates how to secure remote access to a Windows Virtual Machine in Microsoft Azure using Azure Bastion and Network Security Groups (NSGs).

The objective was to reduce direct exposure of the virtual machine to the internet while maintaining secure remote administrative access through Azure Bastion.

## Problem Statement

Exposing Remote Desktop Protocol (RDP) port 3389 directly to the internet increases the attack surface of a cloud-hosted Windows virtual machine.

This project addresses that risk by implementing Azure Bastion as a controlled remote access solution and configuring NSG rules to restrict inbound RDP traffic.

## Architecture Diagram

The architecture illustrates the Azure Virtual Network, separate VM and Bastion subnets, NSG rules, and secure remote access flow.

![Azure Bastion Secure VM Architecture](architecture/azure-network-architecture.png)

## Technologies Used

- Microsoft Azure
- Azure Virtual Machines
- Azure Bastion
- Virtual Networks (VNet)
- Network Security Groups (NSG)
- Windows Server
- Remote Desktop Protocol (RDP)

## Implementation Highlights

- Created a dedicated Azure Resource Group.
- Deployed a virtual network and subnets.
- Provisioned a Windows virtual machine.
- Created a dedicated AzureBastionSubnet.
- Deployed Azure Bastion.
- Configured NSG rules to permit RDP traffic from the Bastion subnet.
- Troubleshot connectivity issues.
- Successfully established remote access through Azure Bastion.
- Hardened the network access configuration.
- Deleted the project resources during cleanup.

## Security Outcome

Successfully established remote administrative access through Azure Bastion and implemented network-level restrictions on RDP traffic.

The project demonstrates practical knowledge of Azure networking, secure remote administration, NSG configuration, cloud security hardening, and resource lifecycle management.

## Documentation

See [PROJECT-DOCUMENTATION.md](PROJECT-DOCUMENTATION.md) for the complete implementation process, configuration details, troubleshooting, security decisions, and cleanup procedures.

## Disclaimer

This project was conducted in a personal Azure lab environment for educational and portfolio purposes. No production infrastructure was involved.
