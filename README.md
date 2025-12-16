# WordPress Deployment Automation

## Overview
This repository serves as the main entry point for an automated WordPress deployment project.
It brings together infrastructure provisioning, configuration management, CI/CD pipelines,
monitoring and backups into a single, organized workflow.

The goal of the project is to demonstrate how a WordPress environment can be deployed,
updated, tested, monitored, and scaled with minimal manual intervention.

## Repository structure
This repository does not contain infrastructure or configuration code directly.
Instead, it links and coordinates multiple specialized repositories.

- Infrastructure provisioning (GCP / Pulumi)
- Configuration management (Ansible)
- CI/CD pipelines (Jenkins)
- Monitoring and logging (OpenSearch)
- Backup automation

## Core repositories

### Infrastructure provisioning (Pulumi)
https://github.com/alper4283/pulumi_gcp

Responsible for:
- Creating Google Cloud resources
- Networking and firewall configuration
- VM provisioning

### Configuration management (Ansible)
https://github.com/alper4283/gcp_wp_deployment

Responsible for:
- Installing and configuring WordPress
- Managing system packages and services
- Database and application setup

## Project scope
This project is designed as a **demonstration and learning project**.
Some steps (such as credential creation or webhook registration) are intentionally kept manual
for security and clarity.

Further automation, CI/CD pipelines, monitoring, backup strategies, and performance results
are documented in the following sections of this repository.
