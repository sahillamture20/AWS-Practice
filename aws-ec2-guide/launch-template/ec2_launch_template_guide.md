# AWS EC2 Launch Template Demo

## Overview

This guide provides a step-by-step walkthrough for creating and launching an EC2 instance using a launch template in AWS, making it ideal for beginners and those looking to streamline their instance management. It covers essential configurations, such as selecting the operating system, instance type, and security settings, ensuring users can customize their setup according to their needs.

By following this guide, users can efficiently deploy instances while gaining a better understanding of AWS EC2 functionalities.

---

## Part 1: Creating a Basic Launch Template

### Step 1: Navigate to EC2
Click **"EC2"** in the AWS console to access the EC2 dashboard.

### Step 2: Access Launch Templates
Click **"Launch Templates"** in the left navigation menu.

### Step 3: Create New Template
Click **"Create launch template"** to begin the configuration process.

### Step 4: Name Your Template
- **Launch template name:** `basic-instance-template`
- Add a description about the template's purpose.

### Step 5: Select Operating System
Click **"Quick Start"** to view and select different operating systems.

### Step 6: Choose OS
Click **"Amazon Linux"** or any other OS you prefer.

### Step 7: Configure Instance Type
Under **"Instance type"**, click **"Don't include in launch template"** to select a different instance type according to your needs.

### Step 8: Select Instance Type
Select **"t2.micro"** as the instance type (eligible for AWS free tier).

### Step 9: Key Pair Selection
For **"Key pair"**, you can:
- Select an existing key pair from the dropdown
- Click **"Create new key pair"** to generate a new one

### Step 10: Choose Your Key Pair
Select your preferred key pair from the available options (e.g., `ins-key-4`).

### Step 11: Network Settings
Under **"Network settings"**:
- Keep **"Subnet"** and **"Availability Zone"** at defaults for practice (or customize if familiar)
- For **"Security groups"**, select an existing one or click **"Create security group"** to build a new one

### Step 12: Select Security Group
Choose a security group that's already present on your account.

### Step 13: EBS Volume Configuration
Under **EBS Volumes**, click to modify the EBS volume settings.

### Step 14: Volume Type Selection
Under **"Volume type"**, click **"gp3"** to view different volume type options.

### Step 15: Choose Volume Type
Click **"General purpose SSD (gp2)"**. This option is selected due to IOPS cost considerations.

### Step 16: Create Template
Review the entire configuration and click **"Create launch template"**.

---

## Part 2: Launching an Instance from Template

### Step 17: Return to Launch Templates
Click **"Launch templates"** to view all available templates.

### Step 18: Select Your Template
Find the launch template you just created and click the checkbox next to it.

### Step 19: Open Actions Menu
Click **"Actions"** to view available operations.

### Step 20: Launch Instance
Click **"Launch instance from template"**.

### Step 21: Confirm Launch
Click **"Launch instance"** to create the EC2 instance.

### Step 22: View Instance
Click on the instance ID (e.g., `i-0cf420406a398de19`) to view details of the newly created instance.

### Step 23: Navigate to Instances
Click **"Instances"** to view all running instances.

### Step 24: Connect to Instance
Select the instance you just created and click **"Connect"**.

### Step 25: Choose Connection Method
Click **"EC2 Instance Connect"**.

### Step 26: Establish Connection
Scroll down and click **"Connect"**.

### Step 27: Connected!
You are now connected to your EC2 instance via the browser-based terminal.

---

## Part 3: Template Versioning - Adding a Web Server

In this section, we'll update our launch template with a web server script (Apache HTTPD) to create Version 2. Then, we'll launch an EC2 instance that comes fully pre-configured with a running web server.

### Step 28: Navigate to Launch Templates
Click **"Launch Templates"** in the EC2 console.

### Step 29: Select Template
Click the checkbox next to your template.

### Step 30: Open Actions
Click **"Actions"** to view options.

### Step 31: Modify Template
Click **"Modify template (Create new version)"**.

### Step 32: Add Version Description
Click the **"Template version description"** field and enter a description like: `A web server template`

### Step 33: Select Source Template
Click **"Source template"** section.

### Step 34: Choose Base Version
Click on the dropdown to select the base template for your new version.

### Step 35: Select Version 1
Click **"1 (Default)"** as your source version.

### Step 36: Expand Advanced Details
Scroll down and click to expand the advanced details section.

### Step 37: Add User Data Script
Click the **"User data - optional"** field and paste the following web server installation script:

```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

This script will:
- Update the system packages
- Install Apache HTTP Server (httpd)
- Start the web server
- Enable it to start automatically on boot
- Create a simple HTML page displaying the hostname

### Step 38: Create New Version
Click **"Create template version"**.

### Step 39: Return to Templates
Click **"Launch templates"** to view your updated template.

### Step 40: Open Actions
Click **"Actions"** on your template.

### Step 41: Launch from Template
Click **"Launch instance from template"**.

### Step 42: Select Template Version
Click the version dropdown to choose which template version to use.

### Step 43: Choose Version 2
Click **"2"** (A web server template) to launch with the web server configuration.

### Step 44: Launch Instance
Scroll down and click **"Launch instance"**.

### Step 45: Navigate to EC2
Click **"EC2"** to return to the main EC2 dashboard.

### Step 46: View Instances
Click **"Instances"** to see all running instances.

### Step 47: Connect to New Instance
Select the newly created instance and click **"Connect"**.

### Step 48: Use Instance Connect
Select **"EC2 Instance Connect"** and click **"Connect"**.

### Step 49: Verify Web Server
The web server has been created with an `index.html` file in `/var/www/html/`.

---

## Task: Verify Web Accessibility

Once your instance is launched and running:

1. Open a web browser
2. Navigate to: `http://[your-instance-public-IP]`
3. You should see the "Hello World" message with your instance hostname

**Troubleshooting:** If the index.html page doesn't load, you may need to:
- Create **version 3** of the template
- Add security group tweaks (e.g., add an inbound HTTP rule allowing port 80)
- Relaunch the instance with the updated configuration

---

## Key Concepts Summary

### Launch Templates
- Reusable configurations for EC2 instances
- Store instance settings like AMI, instance type, security groups, and more
- Support versioning for easy updates and rollbacks

### Template Versioning
- Create multiple versions of the same template
- Each version can have different configurations
- Useful for testing changes or maintaining different deployment scenarios

### User Data Scripts
- Bash scripts that run when an instance first launches
- Automate instance setup and configuration
- Perfect for installing software and configuring services

---

## Best Practices

1. **Free Tier:** Use `t2.micro` instances to stay within AWS free tier limits
2. **Security Groups:** Always configure security groups based on the principle of least privilege
3. **Key Pairs:** Keep your private keys secure and never share them
4. **EBS Volumes:** Choose volume types based on your performance and cost requirements
5. **Documentation:** Always add descriptive names and descriptions to templates and versions

---

## Additional Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [EC2 Launch Templates User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html)

---

*Made with guidance from [Scribe](https://scribehow.com)*