# AWS VPC with Nginx as Public and Private Load Balancers

This repository demonstrates the implementation of an AWS VPC (Virtual Private Cloud) architecture where Nginx is utilized as both a load balancer and a traffic router across public and private subnets. The setup as shown in figure 1 includes:

- **VPC Configuration:** A VPC that comprises two public subnets and two private subnets, designed to securely manage and route traffic.
- **Nginx as Public Load Balancer:** An instance running Nginx is deployed in the public subnets. This instance acts as a load balancer, routing incoming traffic between the two public subnets and efficiently managing traffic distribution.
- **Nginx as Private Load Balancer:** In the private subnets, another instance with Nginx is configured as a load balancer. This instance distributes traffic among multiple private instances, ensuring optimized resource utilization and high availability.
- **Traffic Flow:** When you initially access the public Nginx instance, it routes the request to one of the public instances. These public instances then route the request to the private Nginx load balancer, which further directs the request to a private instance. The initial response will show the IP address of the private instances, demonstrating the complete routing process.

### Key Features
- **Integrated Nginx Load Balancing:** Nginx is used for load balancing in both public and private subnets, ensuring consistent traffic management throughout the architecture.
- **Secure and Scalable Architecture:** The design promotes security by separating public and private subnets, while Nginx provides the flexibility to scale.
- **Comprehensive Traffic Routing:** The architecture supports seamless traffic flow from public entry points to private resources, enhancing operational efficiency.
<p align="center">
  <img src="/images/Arch.drawio.png" alt="AWS VPC Architecture Diagram" />
</p>

<p align="center">
  <em>Figure 1: AWS VPC Architecture with Nginx Routing and Load Balancing</em>
</p>


### Prerequisites
- An AWS account with necessary permissions.
- Basic understanding of AWS VPC, subnets, security groups, and EC2 instances.
- Basic knowledge of Nginx configuration

### Implementation Steps
1. Create VPC and Subnets:
   - Create a VPC with a suitable CIDR block.
   - Create two public subnets in different availability zones.
   - Create two private subnets in different availability zones.
 
2. Create Internet Gateway:
   - Create an internet gateway.
   - Attach the internet gateway to the VPC.
3. ر
3. Create NAT Gateway:
   - Create a NAT gateway in a public subnet for internet access to private instances.
    
4. Create Route Tables:
   - Create two route tables: one for public subnets and one for private subnets.
   - Associate public route table with public subnets.
   - Associate private route table with private subnets.
   - Create a route in the public route table to the internet gateway.
   - Create a route in the private route table to the NAT gateway.

<p align="center">
  <img src="/images/VPC.png" alt="AWS VPC Architecture Diagram" />
</p>

<p align="center">
  <em>Figure 2: AWS VPC </em>
</p>

5. Create Security Groups:
   - Create a security group for public Nginx allowing inbound traffic on port 80.
   - Create a security group for private Nginx allowing inbound traffic from public subnets on a specific port.
   - Create a security group for private EC2 instances allowing inbound traffic from the private Nginx instance on a specific port.
6. Launch EC2 Instances:
   - Launch an EC2 instance in a public subnet with the public Nginx security group.
   - Launch an EC2 instance in a private subnet with the private Nginx security group.
   - Launch multiple EC2 instances in private subnets and public subnets
   - Use user data to install Nginx during instance boot.
     ```sh
     #!/bin/bash
     yum update -y
     yum install -y nginx
     systemctl start nginx
     systemctl enable nginx
     ```
  - edit index file of the private instances with the private ips
    ```sh
     #!/bin/bash
     yum update -y
     yum install -y nginx
     systemctl start nginx
     systemctl enable nginx
     TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
     curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/local-ipv4 > /usr/share/nginx/html/index.html
     ```

7. Configure Nginx
   - Access the EC2 instances via SSH
   - Configure Nginx for load balancing on the public and private instances as needed. This includes creating virtual servers, upstreams, and server blocks.  
<p align="center">
  <img src="/images/LB front.png" alt="Config file of public load balancer" />
</p>

<p align="center">
  <em>Figure 3: Config file of the public instance that works as a load balancer </em>
</p>



<p align="center">
  <img src="/images/public instance config file.png" alt="Config file of public instances" />
</p>

<p align="center">
  <em>Figure 4: Config file of the public instances that will route to the private instance which will work as load balancer</em>
</p>



<p align="center">
  <img src="/images/LB BACK.png" alt="Config file of privte load balancer" />
</p>

<p align="center">
  <em>Figure 5: Config file of the private instance that works as a load balancer </em>
</p>


8. Testing
   - Access the public Nginx instance's IP address from the internet.
   - Verify that the request is routed to a private EC2 instance. "The expected output should resemble the provided screenshots."
  
<p align="center">
  <img src="/images/output 1.png" alt="Output" />
</p>

<p align="center">
  <img src="/images/output2.png" alt="Output" />
</p>
