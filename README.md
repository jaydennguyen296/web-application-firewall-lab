# Web Application Firewall Lab

A virtual Web Application Firewall (WAF) lab using Ubuntu, Kali Linux, and a Safeline WAF appliance.

## Overview

The lab consists of three main components:

- **Kali Linux (Attacker Machine)**
  - Used to perform security testing, including SQL injection, XSS, command injection, and other common web attacks.

- **Safeline WAF (Firewall Layer)**
  - Runs on the Ubuntu server inside a Docker container.
  - Acts as a reverse proxy, inspecting incoming traffic and blocking suspicious requests.

- **Ubuntu Server Hosting DVWA**
  - Hosts DVWA, MySQL, Apache, and PHP.
  - This machine serves as the vulnerable target application, receiving only filtered traffic through the WAF.

- A custom DNS entry was configured so the application is accessed using the domain name `myweb.testing`, simulating a real website.

## Lab Architecture

Traffic flows through the environment in the following order:

1. The attacker machine (Kali Linux at `192.168.0.203`) sends requests to the domain name `myweb.testing`.
2. The domain is mapped using the local hosts file on both Kali and Ubuntu to the IP address `192.168.0.28`.
3. All requests reach the Safeline WAF, which sits in front of the web server as a reverse proxy.
4. Safeline analyzes each request. If the request is malicious, it blocks it and logs the attack. If the request is clean, Safeline forwards it to the Apache server on Ubuntu.
5. The Ubuntu server (`192.168.0.28`) runs DVWA and responds to the request.
6. The response passes back through the WAF and returns to Kali.

### Traffic Flow Diagram

```
Kali (attacker) → myweb.testing → Safeline WAF → Apache2 on Ubuntu → DVWA → Kali
```

This setup allows realistic security testing while ensuring that all traffic is monitored and filtered by the WAF.

## Technologies Used

- **Ubuntu Server** - Hosting Apache, PHP, MySQL, and DVWA
- **Kali Linux** - Used for penetration testing
- **Safeline WAF** - Running in Docker
- **Docker Engine** - For containerization
- **Apache2** - Web Server
- **PHP** - For DVWA backend processing
- **MySQL** - Database for DVWA data storage
- **Local DNS** - Using the hosts file to simulate domain resolution
- **VirtualBox Bridged Networking** - To operate both VMs on the same LAN

## Setup Steps

### 1. Ubuntu Web Server Setup

Apache and PHP were installed on Ubuntu to serve the DVWA web application. This provided the web server environment needed to run PHP-based applications.

### 2. MySQL Database Configuration

- MySQL was installed and configured.
- A dedicated DVWA database and user account were created so DVWA could connect and store its data securely.

### 3. DVWA Installation and Configuration

- DVWA was downloaded into the Apache web directory.
- The DVWA configuration file was updated to match the database details created earlier.
- Apache was then restarted so DVWA could run properly.

### 4. Installing Docker

- Docker was installed on Ubuntu to provide a containerized environment for Safeline WAF.
- This allowed the WAF to run independently of the host system.

### 5. Running Safeline WAF inside Docker

- Safeline was deployed using a Docker container.
- The Safeline dashboard became accessible on port `9443` via the IP address of the Ubuntu server (`192.168.0.28`).
- The container also exposed ports needed to act as a reverse proxy for web traffic.

### 6. Configuring Local DNS with the Hosts File

- The domain name `myweb.testing` was added manually to the hosts file on both Kali and Ubuntu.
- This caused the name to resolve to the Ubuntu server's IP address.
- As a result, DVWA could be accessed using a domain rather than a raw IP address.

### 7. Adding the Protected Site in Safeline WAF

- The domain `myweb.testing` was added as a protected website in Safeline.
- The WAF was configured to forward allowed traffic to the DVWA server on port `80`.
- From this point forward, all traffic to the domain passed through Safeline.

### 8. Testing the WAF Using Kali Linux

- Kali Linux was used to simulate realistic attacks such as SQL injection, XSS, and command injection.
- Safeline successfully logged and blocked these attempts, demonstrating that the WAF was functioning correctly.

## Testing and Results

Using Kali, multiple categories of attacks were attempted against DVWA through the domain `myweb.testing`. Safeline WAF detected and blocked:

- SQL injection payloads
- Cross-site scripting (XSS) attempts
- Command injection strings
- Suspicious URL patterns
- Dangerous request headers

All blocked requests were logged in the Safeline dashboard, confirming that the WAF was effectively protecting the DVWA server.

## What This Project Demonstrates

- How to deploy a WAF using Docker
- How reverse-proxy-based firewalls protect web applications
- How to host DVWA in a controlled lab environment
- How to simulate attacks from Kali Linux
- How to route traffic using custom DNS entries
- How security logs provide insight into attack patterns

This project showcases practical cybersecurity skills, including web security, Linux administration, virtualization, and network monitoring.