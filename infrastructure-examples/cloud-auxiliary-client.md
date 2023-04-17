---
description: Notes on the configuration of the Cloud infrastructure.
---

# ☁ Cloud Auxiliary Client

{% hint style="info" %}
The Cloud Auxiliary Client is used to support and enhance the reliability and effectiveness of the DVT validator. It is not a critical part of the system, and if it fails, the validator will continue to perform duties with the two remaining participants. More details on why a Cloud Auxiliary Client is used can be found [here](../the-evolution-of-solo-staking.md#step-4-obol-dvt-validators-goerli-here-today).
{% endhint %}

AWS was chosen as the Cloud provider due to its wide range of geographic availability and its customizability. Any other Cloud provider could have been chosen.

### AWS EC2 Instance

| Attribute              | Value    |
| ---------------------- | -------- |
| Instance type          | t2.small |
| Memory                 | 2GB      |
| SSD                    | 8GB      |
| Estimated Monthly Cost | $30 USD  |

### Security Group

Create a new security group so that EC2 is on an isolated subnet. This isolates the traffic to the instance from any other EC2s that run on the same AWS account.

### Termination Protection

Enable termination protection to ensure the instance is not accidentally terminated.

### Elastic IP Address

An elastic IP must be allocated and assigned to the EC2 instance. This acts as a static IP that persists between instance shutdowns and restarts.

### Monitoring and Alarms

Set up monitoring and alarms for the EC2 to alert when the system becomes unresponsive, it is nearly out of memory, extended high CPU usage, etc.
