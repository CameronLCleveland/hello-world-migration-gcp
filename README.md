# Hello World Application Cloud Migration Strategy to GCP

**Author:** Cameron Cleveland
**Date:** 08/29/2025
**Project:** Hello World Cloud Migration

## Project Overview
This project outlines the migration strategy for a public-facing "Hello World" application from an on-premises data center to Google Cloud Platform (GCP). The client's top priorities are achieving **minimal downtime** during the migration and ensuring a **highly secure** final architecture. This document details the current state, the proposed future state on GCP, and the roadmap to get there.

## Current State Analysis

The following diagram illustrates the application's existing on-premises network topology, which is functional but carries significant risk and operational overhead.

![Hello World Current On-Premises Architecture](diagrams/Cloud-Migration-HW-Before.jpeg)

### Diagram Explanation & Key Issues:
*   **Single Points of Failure:** The architecture relies on a **single database server** and a **single load balancer**. The failure of either component would cause complete application downtime.
*   **Manual Operational Overhead:** Key processes like **weekly backups** are manual, risking data loss and consuming valuable IT resources. The entire environment is managed via **"Click-Ops,"** leading to slow, inconsistent configurations and deployments.
*   **Lack of Scalability:** The **static web servers** cannot automatically scale to handle increases in traffic. Scaling requires manual provisioning of new hardware or virtual machines, which is a slow process that cannot respond to sudden traffic spikes.
*   **Flat Network Security:** All devices reside on a **flat network** behind a single firewall. This provides no internal segmentation, meaning a breach on one server could easily lead to a compromise of the entire environment.

## Future State Recommendation: Google Cloud Platform Architecture

The proposed future state leverages Google Cloud Platform to create a resilient, automated, and secure infrastructure that directly addresses the limitations of the current environment.

![Hello World Future State on GCP](diagrams/GCP-HW-After.jpeg)

### Diagram Explanation & Networking Choices:

This architecture is built on Google Cloud's scalable and secure foundation.

**1. Reliability & High Availability:**
*   **Managed Instance Groups (MIGs):** The application will run on Compute Engine instances in a Managed Instance Group, which provides autoscaling and auto-healing. If an instance fails, the MIG automatically recreates it.
*   **Multi-Zone Deployment:** The MIG and Cloud SQL instance are deployed across multiple zones within a region to protect against a single zone failure.
*   **Global HTTPS Load Balancer:** Provides a single global IP and automatically routes user traffic to the nearest healthy backend instance, ensuring high availability and low latency.

**2. Security - A Top Priority:**
*   **Cloud Armor:** Configured with the Load Balancer to defend against DDoS and other web-based attacks (like SQL injection and XSS).
*   **VPC Network with Firewall Rules:** The infrastructure is deployed within a Google VPC with finely tuned firewall rules that segment traffic between public and private subnets, following the principle of least privilege.
*   **Identity and Access Management (IAM):** Provides granular control over who has access to which resources, ensuring secure administration.
*   **Secure Remote Access:** Admin access to Compute Engine instances is secured through **Identity-Aware Proxy (IAP)**, eliminating the need to open SSH/RDP ports to the public internet.

**3. Operational Excellence & Cost Optimization:**
*   **Fully Managed Database with Cloud SQL:** The database is migrated to Cloud SQL, which automates backups, updates, patches, and failovers. A high-availability configuration provides automatic failover to a standby instance.
*   **Automated Operations:** **Cloud Monitoring** and **Cloud Logging** provide full observability, while automated backups to **Cloud Storage** ensure data durability and easy recovery.

## Recommended Migration Strategy

To achieve minimal downtime, a **lift-and-shift** approach using **Migrate for Compute Engine** is recommended.

1.  **Replication:** Migrate for Compute Engine will be installed on the on-premises servers to continuously replicate them into a staging area within Google Cloud.
2.  **Testing:** The replicated servers will be launched in an isolated VPC for testing and validation without impacting the live production application.
3.  **Cutover:** Once validated, a final incremental sync will be performed. DNS records will then be updated to point the application's domain name to the new Global HTTPS Load Balancer, completing the migration with minimal downtime.

## Conclusion
This migration transforms a rigid and manually-intensive on-premises operation into a scalable, self-healing, and secure system on Google Cloud Platform. By leveraging fully managed services, we significantly reduce operational overhead while simultaneously improving performance, security, and reliability.
