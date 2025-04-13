# ☕ Utilization and Cost Optimization for Café Web App

## 📘 Project Overview

In this project, I took on the role as a cloud engineer at Café, to optimize AWS service usage and reduce monthly costs. The Café web app originally hosted a local MariaDB database on its EC2 instance. However, after migrating the database to Amazon RDS, the original EC2 instance still consumed unnecessary resources. My task was to uninstall the unused database and downsize the EC2 instance to save on cost and optimize usage.

---

## 🎯 Objectives

By the end of this project, I was able to:

- Optimize an EC2 instance by removing unnecessary services and downsizing its instance type.
- Use AWS CLI for EC2 management and automation.
- Use the AWS Pricing Calculator to estimate pre- and post-optimization costs.
- Demonstrate measurable monthly cost savings.

---

## 🧩 Project Scenario

After migrating Café's local database to **Amazon RDS**, I noticed optimization opportunities. The EC2 instance running the Café web app still had the unused local MariaDB database installed, and it was overprovisioned with a **t3.small** instance type. My job was to:

- Uninstall the decommissioned MariaDB from the EC2 instance.
- Change the instance type to **t3.micro**.
- Validate that the web app continues to run correctly.
- Use the AWS Pricing Calculator to estimate before and after costs.

---

## ✅ Task 1: Optimize the Website to Reduce Costs

### 🖥️ Task 1.1: Connect to the Café EC2 Instance via SSH

![image](https://github.com/user-attachments/assets/c078102d-eb9b-4c24-a34f-cf318b69c19d)

I connected to the **CaféInstance** using the provided `.pem` key:

```bash
chmod 400 oukeyfile.pem
ssh -i oukeyfile.pem ec2-user@35.91.165.46
```

![image](https://github.com/user-attachments/assets/38a3b4db-dfc0-4e03-ba7c-574adea085d3)

Then I configured the AWS CLI environment:

![image](https://github.com/user-attachments/assets/9bf9caab-1cc5-4763-b678-3594a2fa8064)

### 🖥️ Task 1.2: Connect to the CLI Host Instance via SSH and cofigured the AWS CLI environment with the same steps and previous task, but for a different instance

![image](https://github.com/user-attachments/assets/ba7d5393-3302-490a-b387-331cf7e12129)

![image](https://github.com/user-attachments/assets/7da2cf9b-672e-425d-bd6c-7602b4fa52be)

![image](https://github.com/user-attachments/assets/624a3d21-f8b0-44dd-9a3d-4d6bf334096d)


### 🔧 Task 1.3: Uninstall MariaDB and Resize the Instance

On the CaféInstance:

A. Uninstalling MariaDB

```bash
sudo systemctl stop mariadb
sudo yum -y remove mariadb-server
```
![image](https://github.com/user-attachments/assets/8613d481-5a5b-46eb-931e-57368c4def63)

B. Resizing instance to t3.micro

```bash
# Get instance ID
aws ec2 describe-instances \
--filters "Name=tag:Name,Values= CafeInstance" \
--query "Reservations[*].Instances[*].InstanceId"

# Stop the instance
aws ec2 stop-instances --instance-ids i-03de6614b8f57118f

# Resize instance to t3.micro
aws ec2 modify-instance-attribute \
--instance-id i-03de6614b8f57118f \
--instance-type "{\"Value\": \"t3.micro\"}"

# Start the instance
aws ec2 start-instances --instance-ids i-03de6614b8f57118f

# Verify instance is running
aws ec2 describe-instances \
--instance-ids i-03de6614b8f57118f \
--query "Reservations[*].Instances[*].[InstanceType,PublicDnsName,PublicIpAddress,State.Name]"
```

![image](https://github.com/user-attachments/assets/f4100ecc-be73-4897-b2e6-652cbf9873dc)
![image](https://github.com/user-attachments/assets/2958238d-65c6-4ecd-a1a2-10e3619de6a6)
![image](https://github.com/user-attachments/assets/1dedf2d9-7913-419a-874b-381af0c05f3e)
![image](https://github.com/user-attachments/assets/739e1394-ea62-4b46-9db4-848be93623bb)
![image](https://github.com/user-attachments/assets/44b5c842-1211-4678-9977-a8843dbc2eda)

✅ New public DNS: http://ec2-52-10-57-6.us-west-2.compute.amazonaws.com/cafe

I accessed the URL in a browser and confirmed that the Café web app was running properly after optimization.

![image](https://github.com/user-attachments/assets/bfcb9925-1a0f-45cd-81c9-ec26212239fa)


## 💸 Task 2: Estimate Costs Using AWS Pricing Calculator

### 💰 Task 2.1: Estimate Costs Before Optimization

I used the [AWS Pricing Calculator](https://calculator.aws) to estimate the monthly cost before optimization:

EC2 (t3.small), 40GB EBS

![image](https://github.com/user-attachments/assets/3fc3cc94-0b14-4713-8d4d-66b05cd75e29)

![image](https://github.com/user-attachments/assets/1b6a93ac-0a50-4e7d-941e-a41f7e279a2e)

![image](https://github.com/user-attachments/assets/2b238556-11d9-4617-9690-e2551d16dffd)

![image](https://github.com/user-attachments/assets/1572aa71-872d-4c39-beec-ea96ad523c7a)

![image](https://github.com/user-attachments/assets/da4768f0-d4c9-4039-b625-b7423aaad80a)

RDS (db.t3.micro), 20GB

![image](https://github.com/user-attachments/assets/c2363796-8b03-4807-a224-e8eaaad902aa)

![image](https://github.com/user-attachments/assets/8ae8f21b-35e4-42b7-9df2-8df07987c35f)

![image](https://github.com/user-attachments/assets/2c5f6bbb-b91d-4e9c-af7c-2f127c83404f)

![image](https://github.com/user-attachments/assets/9d30c833-4395-4bbc-a59c-b4f6564a8bde)


### 🔗 Estimate: [Before Optimization Calculator Link](https://calculator.aws/#/estimate?id=8c4d80a88610776e2483f79ab8a505f7fec4df39)

![image](https://github.com/user-attachments/assets/ea3a471b-1323-42ba-8ead-afb42a20be31)

**💵 Total: $74.04/month**

### 💡 Task 2.2: Estimate Costs After Optimization

I adjusted the EC2 instance type to t3.micro and reduced EBS storage to 20GB.

![image](https://github.com/user-attachments/assets/d187b648-18c3-4a00-a47b-a3d44e0a3630)
![image](https://github.com/user-attachments/assets/6235f1a0-6a49-40f4-b533-4144fcececf6)

### 🔗 Estimate: [After Optimization Calculator Link](https://calculator.aws/#/estimate?id=252015d7bec15dcb78acff8002ecf5bc5f8aaddc)

![image](https://github.com/user-attachments/assets/1d428df8-444b-4c6e-9441-868706aa7858)

**💵 Total: $64.45/month**


### 🧮 Task 2.3: Projected Monthly Savings

#### 💵 Before Optimization Monthly Costs:
| Service        | Monthly Cost |
|----------------|--------------|
| Amazon RDS     | $54.86       |
| Amazon EC2     | $19.18       |
| **Total**      | **$74.04**   |

#### 💵 After Optimization Monthly Costs:
| Service        | Monthly Cost |
|----------------|--------------|
| Amazon RDS     | $54.86       |
| Amazon EC2     | $9.59        |
| **Total**      | **$64.45**   |


**📉 Savings: $9.59/month**

> 💡 **Note**: Pricing is current as of March 2025 and for demonstration purposes only. For current pricing, refer to the official [AWS Pricing page](https://aws.amazon.com/pricing/).


## 🏁 Conclusion

By removing the decommissioned MariaDB and resizing the EC2 instance, I was able to reduce monthly AWS service costs by nearly **$10 per month**, demonstrating the impact of small but effective optimizations. This project showcases:

- Cost-effective resource management

- Real-world AWS pricing estimation and savings reporting

*These types of optimizations scale well and can produce significant long-term cost reductions for growing infrastructure.*
