# AWS VPC Peering with Bastion Host

A hands-on AWS networking project demonstrating secure communication between two Virtual Private Clouds (VPCs) using **VPC Peering, Amazon EC2, Route Tables, Security Groups, and a Bastion Host**.

## 🏗️ Architecture Diagram

![AWS VPC Peering Architecture](architecture.png)

### Project Workflow

**Local Computer → Bastion Host → App-A → VPC Peering → App-B → Bidirectional Connectivity Testing**

## 🎯 Project Objectives

- Create two isolated VPCs with separate CIDR blocks.
- Configure public and private subnets.
- Establish a VPC Peering connection.
- Configure route tables for private communication between VPCs.
- Use a Bastion Host to access private EC2 instances through SSH.
- Verify connectivity using private IP addresses.

## 🛠️ AWS Services Used

| Service | Purpose |
|---|---|
| Amazon VPC | Create isolated virtual networks |
| Subnets | Separate public and private resources |
| Amazon EC2 | Host the Bastion Host and application instances |
| VPC Peering | Enable private communication between VPCs |
| Route Tables | Direct network traffic to the appropriate destination |
| Security Groups | Control inbound and outbound traffic |
| Internet Gateway | Provide internet connectivity to the public subnet |
| SSH | Securely access EC2 instances |

## 🌐 Network Configuration

### VPC A

| Resource | Configuration |
|---|---|
| VPC Name | `ewb-vpc-a` |
| CIDR Block | `10.10.0.0/16` |
| Public Subnet | `10.10.1.0/24` |
| Private Subnet | `10.10.2.0/24` |
| Bastion Host | `ewb-bastion` |
| Bastion Private IP | `10.10.1.10` |
| App-A | `ewb-app-a` |
| App-A Private IP | `10.10.2.10` |

### VPC B

| Resource | Configuration |
|---|---|
| VPC Name | `ewb-vpc-b` |
| CIDR Block | `10.20.0.0/16` |
| Private Subnet | `10.20.1.0/24` |
| App-B | `ewb-app-b` |
| App-B Private IP | `10.20.1.10` |

**AWS Region:** `ap-south-1` (Mumbai)

## ⚙️ Implementation Steps

### 1. Create Two VPCs

Created two VPCs with non-overlapping CIDR ranges:

- VPC A: `10.10.0.0/16`
- VPC B: `10.20.0.0/16`

### 2. Configure Subnets

Created a public subnet and a private subnet in VPC A, and a private subnet in VPC B.

### 3. Configure the Internet Gateway

Attached an Internet Gateway to VPC A and configured the public route table to allow internet-bound traffic from the public subnet.

### 4. Establish VPC Peering

Created a VPC Peering connection between `ewb-vpc-a` and `ewb-vpc-b`.

### 5. Configure Route Tables

Added routes to direct traffic between the two VPC CIDR blocks through the VPC Peering connection.

| Route Table | Destination | Target |
|---|---|---|
| VPC A Public | `10.20.0.0/16` | VPC Peering |
| VPC A Private | `10.20.0.0/16` | VPC Peering |
| VPC B Private | `10.10.0.0/16` | VPC Peering |

The public route table also contains the default internet route through the Internet Gateway.

### 6. Configure Security Groups

Configured security groups to allow:

- SSH access to the Bastion Host from the authorized client IP.
- SSH access to private instances through the Bastion Host.
- ICMP Echo Request traffic between App-A and App-B.
- Outbound traffic as required for the lab.

### 7. Connect Through the Bastion Host

Used SSH to connect from the local computer to the Bastion Host, then accessed the private EC2 instances using their private IP addresses.

Example SSH command for App-A through the Bastion Host:

```bash
ssh -i ewb-lab.pem \
  -o "ProxyCommand=ssh -i ewb-lab.pem -W %h:%p ec2-user@BASTION_PUBLIC_IP" \
  ec2-user@10.10.2.10
```

Replace `BASTION_PUBLIC_IP` with the Bastion Host's current public IP address. Run the command from your local computer and ensure the PEM key path is correct.

## 🧪 Connectivity Testing

### Test 1: App-A to App-B

Run on App-A:

```bash
ping -c 4 10.20.1.10
```

Expected result: Successful replies from App-B with no packet loss.

### Test 2: App-B to App-A

Run on App-B:

```bash
ping -c 4 10.10.2.10
```

Expected result: Successful replies from App-A with no packet loss.

These tests verify private IP connectivity in both directions when the peering connection, routes, and security rules are correctly configured.

## 📸 Project Screenshots

screenshots/
├── 01-vpc-a.png  <img width="1906" height="846" alt="Screenshot 2026-10-28 014307" src="https://github.com/user-attachments/assets/8185f096-ad2a-49aa-b9a2-5b81358cc060" />

├── 02-vpc-b.png <img width="1915" height="827" alt="Screenshot 2026-10-28 014335" src="https://github.com/user-attachments/assets/5a2c113d-11a2-40d3-95a1-871d91556e85" />

├── 03-vpc-peering-active.png  <img width="1908" height="828" alt="Screenshot 2026-10-28 014410" src="https://github.com/user-attachments/assets/2bc98730-e0ff-4076-a214-4d14aad3332c" />

├── 04-route-table-vpc-a.png  <img width="1910" height="837" alt="Screenshot 2026-10-28 014648" src="https://github.com/user-attachments/assets/fc8ef392-7f47-4298-b2db-828869389fb8" />

├── 05-route-table-vpc-b.png <img width="1907" height="844" alt="Screenshot 2026-10-28 014619" src="https://github.com/user-attachments/assets/e02f7e9d-03f3-43f7-97c4-8e9a9608f6f4" />

├── 06-ec2-instances.png  <img width="1911" height="830" alt="Screenshot 2026-10-28 014729" src="https://github.com/user-attachments/assets/bff85d3f-43d9-407e-8810-7fdf225123a7" />

├── 07-ping- <img width="1890" height="996" alt="Screenshot 2026-10-28 015343" src="https://github.com/user-attachments/assets/6acf9432-8533-49ed-b54b-fa42cbf9de6a" />

```

## 🔐 Security Best Practices

- Restrict SSH access to trusted IP addresses.
- Avoid exposing private EC2 instances directly to the internet.
- Use private IP addresses for communication between peered VPCs.
- Apply least-privilege security group rules.
- Never commit private keys, passwords, or credentials to GitHub.
- Stop unused EC2 instances when the lab is not in use; note that attached storage and certain networking resources may still incur charges.

## 📚 Key Learnings

- AWS VPC networking and subnet design.
- VPC Peering and route table configuration.
- Security group rules and ICMP connectivity.
- Bastion-based SSH access to private instances.
- Troubleshooting network connectivity across VPCs.

## 🚀 Future Improvements

- Automate infrastructure provisioning using Terraform.
- Configure centralized monitoring with Prometheus and Grafana.
- Explore AWS V Systems Manager Session Manager for managed instance access.
- Add infrastructure diagrams and network troubleshooting documentation.

## 👨‍💻 Author

**Rishikant Kumar**

- GitHub: [rishikant0](https://github.com/rishikant0)
- LinkedIn: [rishikantkr](https://www.linkedin.com/in/rishikantkr)

---

⭐ If you find this project useful, feel free to explore the repository and share feedback.
