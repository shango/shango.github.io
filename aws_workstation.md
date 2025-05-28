# Deploy a remote VFX workstation on AWS
---
title: Deploying a Remote VFX Workstation on AWS
layout: page
nav_order: 2
---

Visual effects studios increasingly use cloud-based workstations as an alternative to traditional on-premises setups. This guide shows you how to deploy a high-performance compositing workstation on Amazon Web Services (AWS). You can access your workstation remotely using Windows Remote Desktop Protocol (RDP) and install your preferred compositing software.

## Before you begin

Before you start, verify that you have:

- An active AWS account with billing information
- Administrator access to your AWS account
- An internet connection with at least 15 Mbps download and upload speed
- Basic familiarity with Windows Server administration
- Familiarity with Windows Remote Desktop Protocol (RDP)

This guide is for compositors, pipeline engineers, or technical directors with intermediate to advanced experience in AWS administration, Windows VFX workstation requirements, formatting storage volumes, and Windows PowerShell.

After you complete this guide, you will have:

- Selected and launched an appropriate EC2 instance
- Requested AWS service quota increases
- Deployed a Windows Server AMI and configured system settings
- Configured an Elastic IP for consistent remote access
- Installed NVIDIA GRID drivers

## Request quota increases

Many GPU-enabled instances require quota increases before you can launch them.

1. Sign in to the AWS Management Console and navigate to the **EC2** dashboard.

2. In the upper right corner of the page, choose a geographic region closest to your user base. For example, choose **us-west-1** for United States (N. California).

   ![Region selection dropdown showing 'us-west-1'](../images/f1_set_region.png)
   > *Figure 1. Region set to 'United States (N. California)'*

3. From the **User Account** drop-down menu (labeled with your account name in the top-right corner), choose **Service Quotas**.

   ![User Account dropdown open with Service Quotas link highlighted](../images/f2_user_drop_down_hl.png)
   > *Figure 2. User Account drop-down showing Service Quotas link*

4. On the **Service Quotas** page, select **AWS services** from the left navigation panel.

5. In the search bar, type "EC2" and select **Amazon Elastic Compute Cloud (Amazon EC2)** from the results.

6. In the service quotas search bar, type "G instances" and select **Running On-Demand G and VT Instances**.

   The **Applied account-level quota value** section displays your current quota.

   ![Service Quota display with current value highlighted](../images/f3_service_quota_hl.png)
   > *Figure 3. The current Service Quota value is highlighted*

7. If your current quota is less than 32 vCPUs, click **Request increase at account level**.

8. Set the **Increase quota value** to at least 32 vCPUs to support the g4dn.8xlarge instance.

   ![Quota increase dialog with value set to 8](../images/f4_set_new_quota_hl.png)
   > *Figure 4. Setting quota increase value*

9. Provide a brief justification for your request. For example, type "Deploying VFX workstation for remote production" and submit the request.

AWS typically processes quota increase requests within 24 to 48 hours. You receive an email notification when your request is approved.

## Launch and configure an EC2 Windows Server instance

After your quota increase is approved, you can launch your workstation instance.

1. From the AWS dashboard, navigate to the **EC2** dashboard and click **Launch Instance**.

2. In the **Name and tags** section, enter a descriptive name for your instance. For example, type "VFX-Workstation-01".

3. Under **Application and OS Images (Amazon Machine Image)**, click the **Quick Start** tab, search for "Windows," and select **Microsoft Windows Server 2025 Base**.

   ![Windows Server AMI selection with Windows Server 2025 highlighted](../images/f5_select_win_hl.png)
   > *Figure 5. Microsoft Windows Server 2025 Base selected*

4. Under **Instance type**, search for and select **g4dn.8xlarge**. This instance provides a good balance of GPU power (1 NVIDIA T4 GPU), CPU resources (32 vCPUs), memory (128 GB), and network performance for high-end compositing work.

   ![Instance type selection with g4dn.8xlarge highlighted](../images/f6_instance_type.png)
   > *Figure 6. g4dn.8xlarge instance type selected*

5. Under **Key pair (login)**, create a new key pair:

   - Click **Create new key pair**.
   - Enter a recognizable name. For example, type "vfx-workstation-keypair".
   - Select **RSA** as the Key pair type.
   - Choose **PEM** as the Private key file format.
   - Click **Create key pair**.

   ![Key pair creation dialog](../images/f7_create_key_pairs.png)
   > *Figure 7. Creating a new key pair*

   The private key file (.pem) automatically downloads to your computer. Store this file in a secure location because you need it to retrieve the administrator password for your Windows instance.

6. Under **Network settings**, click **Edit** and configure the following:

   - Leave VPC and subnet at default values unless you have specific networking requirements.
   - Set **Auto-assign public IP** to **Enable**.
   - Under **Firewall (security groups)**, select **Create a new security group**.
   - Name your security group. For example, type "vfx-workstation-sg".
   - Ensure this inbound rule exists: RDP (port 3389) from your IP address.

7. Under **Configure storage**, set up your storage volume:

   - Increase the **Root volume** (C: drive) size from 30 GB to **500 GB**.
   - Leave the volume type as **gp3** (General Purpose SSD).

   ![Storage configuration with root volume](../images/f8_config_storage.png)
   > *Figure 8. Configured root volume*

8. Review your selections and click **Launch instance**.

The instance initializes in approximately five to 10 minutes.

## Allocate and associate an Elastic IP

An Elastic IP provides a static public IP address that ensures your workstation remains accessible at the same address even after restarts.

1. In the **EC2** console, select **Elastic IPs** from the left navigation panel under "Network & Security."

2. Click **Allocate Elastic IP address**.

3. Leave the default settings and click **Allocate**.

4. Select the newly allocated Elastic IP, then click **Actions** > **Associate Elastic IP address**.

5. Under **Resource type**, select **Instance**.

6. Select your VFX workstation instance from the **Instance** drop-down list.

7. Click **Associate**.

## Connect to your Windows instance

Connect to the workstation using Windows Remote Desktop Protocol (RDP).

1. In the **EC2** console, select your running instance.

2. Click **Connect** at the top of the page.

3. Select the **RDP** tab.

4. Click **Get password** and upload your .pem key file when prompted.

5. Copy the decrypted password.

6. Use an RDP client (such as Remote Desktop Connection on Windows) to connect to your instance using:
   - The Elastic IP address you associated with your instance
   - Username: Administrator
   - Password: the decrypted password

7. Accept any security certificates when prompted.

## Install NVIDIA GRID drivers

Install GPU drivers to enable GPU acceleration for your VFX applications.

1. After you connect to your Windows instance, open **Windows PowerShell** as Administrator.

2. Execute the following commands to install the AWS Command Line Interface (CLI):

   ```powershell
   Invoke-WebRequest -Uri "https://awscli.amazonaws.com/AWSCLIV2.msi" -OutFile "AWSCLIV2.msi"
   Start-Process msiexec.exe -Wait -ArgumentList "/i AWSCLIV2.msi"
   ```

3. Configure the AWS CLI with the following command:

   ```powershell
   aws configure
   ```

4. When prompted, enter your AWS log in credentials.

5. For the **Default Region**, enter the region you set when you created the instance.

6. Download the NVIDIA drivers using the following command:

   ```powershell
   aws s3 cp --recursive s3://ec2-windows-nvidia-drivers/latest/ .
   ```

7. After the download completes, navigate to your current directory and run the installation file with a .exe extension.

8. Follow the installation wizard and accept the default options.

9. When installation is complete, restart your instance.

The workstation is now set up and you can install VFX software.

## Optimize costs

Consider these strategies to reduce costs:

- Stop your instance when not in use. You still pay for storage but not compute resources.
- Consider purchasing a Reserved Instance for long-term projects.
- Monitor your AWS billing dashboard regularly.
- Set up billing alerts to notify you of unexpected charges.

## Related topics

- [AWS EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/) (aws.amazon.com)
- [AWS Service Quotas User Guide](https://docs.aws.amazon.com/servicequotas/latest/userguide/) (aws.amazon.com)
- [Windows Server on AWS](https://aws.amazon.com/windows/) (aws.amazon.com)