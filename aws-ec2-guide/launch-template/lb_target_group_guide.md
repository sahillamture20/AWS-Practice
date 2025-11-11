# Create a Load Balancer and Target Group in AWS

## Overview

This guide provides a step-by-step approach to creating a Load Balancer and Target Group in AWS, making it essential for anyone looking to enhance their cloud infrastructure. It simplifies the process, ensuring users can efficiently manage traffic and improve application performance.

By following these instructions, users can gain hands-on experience with AWS services, which is crucial for optimizing resource allocation and scaling applications effectively. Whether you're a beginner or an experienced user, this guide is a valuable resource.

---

## Prerequisites

Before starting this guide, you should have:
- An AWS account with appropriate permissions
- Basic understanding of EC2 instances
- A Launch Template configured with a web server (see Step 2 for reference)

---

## Part 1: Launching EC2 Instances from Template

### Step 1: Navigate to EC2
Open the AWS Console and click **"EC2"** to access the EC2 dashboard.

### Step 2: Access Launch Templates
Click **"Launch Templates"** in the left navigation menu.

**Don't have a Launch Template?** Follow this free step-by-step guide to create one quickly:
📘 [AWS EC2 Launch Template Demo Guide](https://github.com/YOUR-USERNAME/aws-ec2-launch-template-guide)

Once you've created your launch template with a web server configuration, return here to continue!

### Step 3: Select Launch Template
Click the **checkbox** next to your launch template to select it.

### Step 4: Open Actions Menu
Click **"Actions"** to view available operations.

### Step 5: Launch Instance from Template
Click **"Launch instance from template"**.

### Step 6: Select Template Version
Click on the version dropdown to select a different version if needed.

### Step 7: Choose Version 2
Click **"2"** (or your web server version). This version should be configured to automatically set up a web server.

> **Note:** To learn more about template versioning, refer to the Launch Template guide mentioned in Step 2.

### Step 8: Specify Number of Instances
Click the **"Number of instances"** field and enter the total number of instances you want to create.

**Example:** If you want 3 instances total and already have 1 running, create 2 more instances here.

### Step 9: Launch Instances
Click **"Launch instance"** to create your EC2 instances.

### Step 10: View Running Instances
Click **"Instances"** in the left navigation menu to view all your instances.

### Step 11: Name Your Instances
Click on the name field next to each newly created instance.

### Step 12: Assign Descriptive Names
Type names for your instances:
- `LB-webserver-1`
- `LB-webserver-2`
- `LB-webserver-3`

> **Pro Tip:** Use consistent naming conventions to easily identify instances that belong together.

---

## Part 2: Creating a Target Group

Target Groups are used to route requests to registered targets (like EC2 instances) as part of load balancing.

### Step 13: Navigate to Target Groups
Click **"Target Groups"** in the left navigation menu under Load Balancing.

### Step 14: Create Target Group
Click **"Create target group"**.

### Step 15: Choose Target Type
Select **"Instances"** as the target type.

### Step 16: Name Your Target Group
Click the **"Target group name"** field and enter: `LB-practice-TG`

### Step 17: Verify Network Settings
Review and confirm your VPC and network configuration settings.

### Step 18: Configure Protocol and Port
- **Protocol:** Select **"HTTP"**
- **Port:** Enter **80**

### Step 19: Set Protocol Version
Select **"HTTP1"** as the protocol version.

### Step 20: Configure Health Checks
Under **"Health Checks"**, select **"HTTP"** protocol (must match the protocol used by your instances).

> **Important:** Health checks verify that your instances are healthy and can receive traffic.

### Step 21: Proceed to Register Targets
Click **"Next"** to move to the target registration page.

### Step 22: Select Instances
Use the **checkboxes** to select all instances you want to include. In this example, we're selecting all 3 web server instances.

### Step 23: Add to Target Group
Click **"Include as pending below"** to add the selected instances to the target group.

### Step 24: Review Selected Instances
You will see all selected instances listed below. Verify they're correct, then click **"Next"**.

### Step 25: Create Target Group
Review all configurations and click **"Create target group"**.

---

## Part 3: Creating an Application Load Balancer

### Step 26: Navigate to Load Balancers
Click **"Load Balancers"** in the left navigation menu under Load Balancing.

### Step 27: Create Load Balancer
Click **"Create load balancer"**.

### Step 28: Choose Load Balancer Type
Locate the **"Application Load Balancer"** section.

### Step 29: Select Application Load Balancer
Click **"Create"** under Application Load Balancer.

### Step 30: Name Your Load Balancer
Click the **"Load balancer name"** field and enter: `practice-ALB`

### Step 31: Configure Scheme and IP Type
- **Scheme:** Select **"Internet-facing"**
- **IP address type:** Select **"IPv4"**

> **Internet-facing** means the load balancer will be accessible from the internet.

### Step 32: Select Availability Zones
Click the **checkboxes** to select at least **two Availability Zones** and **one subnet per zone**.

> **Best Practice:** Distributing instances across multiple AZs improves availability and fault tolerance.

### Step 33: Configure Security Groups
Click to add a security group.

**⚠️ IMPORTANT NOTE:** Since all 3 instances are web servers, your security group **must** have an inbound rule allowing HTTP traffic (port 80).

**Required Inbound Rule:**
- **Type:** HTTP
- **Protocol:** TCP
- **Port:** 80
- **Source:** 0.0.0.0/0 (or your specific IP range)

### Step 34: Select Target Group
In the **"Listeners and routing"** section, select the target group you created earlier (`LB-practice-TG`).

### Step 35: Create Load Balancer
Review all settings and click **"Create load balancer"**.

---

## Part 4: Testing Your Load Balancer

### Step 36: Return to Load Balancers
Click **"Load Balancers"** to view all load balancers.

### Step 37: Wait for Active State
Select your newly created load balancer and wait until its **State** changes to **"Active"**.

> **Note:** This may take 2-5 minutes. The load balancer needs to complete its initialization and health checks.

### Step 38: Copy DNS Name
Once active, copy the **DNS name** of your load balancer.

**Example DNS name:**
```
practice-ALB-1234567890.us-east-1.elb.amazonaws.com
```

### Step 39: Test Load Balancing
1. Open a new browser tab
2. Paste the DNS name into the address bar
3. Press **ENTER**
4. **Refresh the page multiple times**

Each refresh should show a message from a different server, proving the load balancer is distributing traffic across your instances!

### Step 40: First Request
You'll see a response from the first instance (note the IP address/hostname).

**Example:**
```
Hello World from ip-172-31-xx-xx.ec2.internal
```

### Step 41: Second Request (After Refresh)
Refresh the page to see a response from a different instance.

**Example:**
```
Hello World from ip-172-31-yy-yy.ec2.internal
```

### Step 42: Third Request (After Refresh)
Refresh again to see yet another instance responding.

**Example:**
```
Hello World from ip-172-31-zz-zz.ec2.internal
```

> **What's happening?** The Application Load Balancer is using a round-robin algorithm to distribute incoming requests evenly across all healthy instances in your target group!

---

## How Load Balancing Works

### Request Flow

```
User Browser
    ↓
Application Load Balancer (DNS)
    ↓
Target Group
    ↓
┌─────────┬─────────┬─────────┐
│ Instance│ Instance│ Instance│
│    1    │    2    │    3    │
└─────────┴─────────┴─────────┘
```

### Load Balancing Benefits

1. **High Availability** - If one instance fails, traffic routes to healthy instances
2. **Scalability** - Easily add or remove instances from the target group
3. **Even Distribution** - Requests are distributed across all healthy instances
4. **Health Monitoring** - Automatic health checks ensure only healthy instances receive traffic
5. **SSL/TLS Termination** - Offload SSL decryption from your instances (can be configured)

---

## Key Concepts Explained

### Application Load Balancer (ALB)
- Operates at Layer 7 (Application layer)
- Routes traffic based on content of requests
- Supports HTTP/HTTPS protocols
- Ideal for web applications and microservices

### Target Group
- A logical grouping of targets (EC2 instances, IP addresses, or Lambda functions)
- Performs health checks on registered targets
- Routes requests only to healthy targets
- Can be associated with multiple load balancers

### Health Checks
- Periodic requests sent to targets to verify they're functioning
- **Healthy threshold:** Number of consecutive successful checks before marking healthy
- **Unhealthy threshold:** Number of consecutive failed checks before marking unhealthy
- **Timeout:** Time to wait for a response
- **Interval:** Time between health checks

### Availability Zones (AZ)
- Isolated locations within an AWS Region
- Spreading instances across multiple AZs provides fault tolerance
- Load balancers automatically distribute traffic across all enabled AZs

---

## Troubleshooting

### Load Balancer Shows "Unhealthy" Targets

**Check:**
1. Security group allows traffic on the health check port
2. Web server is running on the instance
3. Health check path is accessible (default: `/`)
4. Instance is in a running state

**Solution:**
```bash
# SSH into the instance and check web server status
sudo systemctl status httpd  # For Amazon Linux
# or
sudo systemctl status apache2  # For Ubuntu
```

### Cannot Access Load Balancer via DNS

**Check:**
1. Load balancer state is "Active"
2. Security group allows inbound HTTP (port 80) traffic
3. At least one target is healthy
4. DNS name is copied correctly (no extra spaces)

### All Requests Go to Same Instance

**Check:**
1. Browser caching - try using incognito/private mode
2. Sticky sessions enabled - check target group attributes
3. Only one target is healthy - check target health status

---

## Best Practices

### 1. Multi-AZ Deployment
Always deploy instances across multiple Availability Zones for high availability.

### 2. Security Groups
- Use separate security groups for load balancers and instances
- Allow only necessary ports (80 for HTTP, 443 for HTTPS)
- Restrict instance security groups to accept traffic only from the load balancer

### 3. Health Check Configuration
```
Healthy threshold: 2
Unhealthy threshold: 2
Timeout: 5 seconds
Interval: 30 seconds
```

### 4. Monitoring
- Enable access logs to track requests
- Set up CloudWatch alarms for:
  - Unhealthy host count
  - Request count
  - Target response time
  - HTTP error rates

### 5. Auto Scaling (Advanced)
Combine load balancers with Auto Scaling Groups to automatically adjust capacity based on demand.

---

## Cleanup (To Avoid Charges)

When you're done practicing, remember to delete resources:

1. **Delete Load Balancer**
   - EC2 → Load Balancers → Select → Actions → Delete

2. **Delete Target Group**
   - EC2 → Target Groups → Select → Actions → Delete

3. **Terminate Instances**
   - EC2 → Instances → Select → Instance State → Terminate

> **Note:** Load balancers incur hourly charges even when not in use!

---

## Cost Considerations

- **Application Load Balancer:** ~$0.0225 per hour + data processing charges
- **EC2 Instances:** Varies by instance type (t2.micro eligible for free tier)
- **Data Transfer:** Charges apply for data transferred out to the internet

**Free Tier:** 750 hours per month of Classic Load Balancer (or 15 hours per day for one month)

---

## Next Steps

### Enhance Your Setup

1. **Add HTTPS Support**
   - Request an SSL/TLS certificate from AWS Certificate Manager
   - Configure HTTPS listener on your load balancer

2. **Implement Auto Scaling**
   - Create an Auto Scaling Group
   - Set scaling policies based on CPU or request count

3. **Configure Custom Health Checks**
   - Create a dedicated health check endpoint
   - Return detailed health status from your application

4. **Set Up CloudWatch Monitoring**
   - Create custom dashboards
   - Configure alarms for key metrics

5. **Use Path-Based Routing**
   - Route different URL paths to different target groups
   - Support multiple applications behind one load balancer

---

## Additional Resources

- [AWS Application Load Balancer Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [Target Groups Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [EC2 Launch Templates Guide](https://github.com/YOUR-USERNAME/aws-ec2-launch-template-guide)

---

## Summary

You've successfully:
- ✅ Launched multiple EC2 instances from a template
- ✅ Created a Target Group with health checks
- ✅ Configured an Application Load Balancer
- ✅ Tested traffic distribution across instances
- ✅ Understood core load balancing concepts

This setup provides the foundation for building highly available, scalable web applications on AWS!

---

*Made with guidance from [Scribe](https://scribehow.com)*