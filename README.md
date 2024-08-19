# AWS VPC with Nginx as Public and Private Load Balancers

This repository demonstrates the implementation of an AWS VPC (Virtual Private Cloud) architecture where Nginx is utilized as both a load balancer and a traffic router across public and private subnets. The setup includes:

- **VPC Configuration:** A VPC that comprises two public subnets and two private subnets, designed to securely manage and route traffic.
- **Nginx as Public Load Balancer:** An instance running Nginx is deployed in the public subnets. This instance acts as a load balancer, routing incoming traffic between the two public subnets and efficiently managing traffic distribution.
- **Nginx as Private Load Balancer:** In the private subnets, another instance with Nginx is configured as a load balancer. This instance distributes traffic among multiple private instances, ensuring optimized resource utilization and high availability.
- **Traffic Flow:** When you initially access the public Nginx instance, it routes the request to one of the public instances. These public instances then route the request to the private Nginx load balancer, which further directs the request to a private instance. The initial response will show the IP address of the private instances, demonstrating the complete routing process.

### Key Features
- **Integrated Nginx Load Balancing:** Nginx is used for load balancing in both public and private subnets, ensuring consistent traffic management throughout the architecture.
- **Secure and Scalable Architecture:** The design promotes security by separating public and private subnets, while Nginx provides the flexibility to scale.
- **Comprehensive Traffic Routing:** The architecture supports seamless traffic flow from public entry points to private resources, enhancing operational efficiency.
