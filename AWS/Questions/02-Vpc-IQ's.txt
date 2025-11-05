1. What is Amazon VPC and why is it used in AWS?
    - Amazon VPC is a private, Isolated Network within Aws. It allows us to launch Aws resources like Ec2 Instances, Databases in a secure environment.
    - with VPC we can control over  network setup like IP address ranges, subnets, route tables and gateways.
    - It helps us to which resource should be public and which resource should be private.
    - We can control traffic using security groups and Network ACL's and also we can connect our VPC to other networks like On-premises through VPN or Direct Connect.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2.  What are the key components of a VPC?
     - The Key components of a VPC are subnets, route tables, security groups, internet gateway, NAT gateway, Network ACLs. 
     * Subnet: Using subnets we can divide the large network into the smaller components and in subnets we have Public, Private subnets.
          - Public subnet: this subnet route to the Internet gateway allowing instances inside it to communicate directly with Internet if they have public IP's or Elastic IP's.
          - Private Subnet: this subnet don't have direct route to the Internet, so instances cannot access the internet directly. If they need internet access to download, updates they
                            can use NAT gateway in public subnet to route the traffic securely.
                            
    * Route Tables: route tables defines how the network traffic is directed within a VPC. it contains set of rules where the network traffic should go.
                   - Each subnet in a VPC is associated with a route table which helps us to send traffic to the right destination.
      Types: There are two types of route table.
      1. Main route table which is created by default and used by all subnets unless we change it.
      2. Custom route table where we create and associate with a specific subnets when we want different routing like separating public and private subnets.

    * Internet Gateway: Internet Gateway is a component in AWS that allows communication between your VPC and internet.
                        - It helps instances in a public subnet send and receive traffic from the internet.
                        - To make a subnet public we attach an internet gateway to the VPC and add a route (0.0.0.0/0) in the route table that points to the IGW.
                        - without an Internet gateway resources inside the VPC cannot access or be accessed by internet directly.

    * NAT Gateway / NAT Instance: A NAT gateway and NAT instance allows resources in a private subnet to access the internet for outbound traffic only such downloading updates and
                                  connecting to external resources.    
                                  - It doesn't allow inbound traffic from the internet so the resources stay private and secure. 
                                  - A NAT device placed inside a public subnet and the private subnet's routes table points internet-bound traffic (0.0.0.0/0),to it.
                                  - The main difference is that NAT Gateway is a managed AWS service (scalable and maintenance-free), while a NAT Instance is a self-managed EC2 instance
                                    performing the same role.   
     
    * Security Group: SG in AWS acts as a virtual firewall that controls inbound and outbound traffic of ec2 instances.
                      - It defines which traffic is allowed to reach or leave instance based on rules we set.
                      - Inbound rules control what traffic is allowed into the instance (for example, allowing SSH on port 22 or HTTP on port 80).
                      - Outbound rules control what traffic is allowed out from the instance (for example, allowing all outbound internet access).
                      - Security Groups are stateful, which means if you allow incoming traffic, the response is automatically allowed out you don’t need a separate outbound rule for it.

   * Network ACL (Access Control List): NACL's are another layer of security in AWS that controls inbound and outbound traffic at subnet level.
                                        - NACL's are stateless which means we must create separate rules for inbound and outbound traffic.
                                        - Each VPC automatically comes with a default NACL, and you can also create custom NACLs for more granular control.

  * Elastic IPs: EIPs are static public IPv4 addresses that you can assign to resources like EC2 instances. They stay the same even if you stop or restart the instance, unlike a normal
                 public IP which can change.
                 - Elastic IPs are useful when you need a fixed public IP address, for example, for hosting a website.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

3. What is the default VPC and how is it different from a custom VPC?
   - A default VPC is automatically created by AWS in each region. It allows you to launch instances quickly that can immediately communicate with the internet.
   - It comes with everything preconfigured including a default route table, security group, network ACL, One public subnet per Availability Zone, and an Internet Gateway attached.
   - A custom VPC is one that we can create manually. we have have full control over the subnets, route tables, gateways, and IP ranges.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4. How do you associate a subnet with a route table?
   - In AWS, each subnet must be associated with a route table to control how traffic is routed.
   - You can associate a subnet with a route table in the VPC console:
     Go to VPC → Route Tables.
     Select the route table you want to use.
     Choose the “Subnet Associations” tab.
     Click “Edit subnet associations” and select the subnets you want to link (IGW or NAT)
     Save the changes.
   - Once associated, the subnet will follow the routes defined in that route table.

   Example:
   - If you associate a subnet with a route table that has a route to an Internet Gateway, it becomes a public subnet.
   - If you associate it with a route table that has a route to a NAT Gateway, it becomes a private subnet

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. What is a VPC peering connection and when should you use it?
   - A VPC Peering Connection is a network connection between two VPCs that allows them to communicate privately as if they were on the same network.
   - It can be used to connect VPCs within the same AWS account or across different accounts and regions.
   - Traffic between peered VPCs stays within the AWS network it does not go over the public internet, which makes it secure and low-latency.
   - use VPC peering when we want resources in different VPCs like EC2 instances, databases, or applications to communicate securely and directly for example, connecting a development VPC
     to a production VPC.
   - You can create up to 125 VPC peering connections per VPC by default in AWS
   - This means a single VPC can be peered with 125 other VPCs at one time. However, this limit can be increased by requesting a service quota increase from AWS if needed.
   - VPC peering is one-to-one it does not support transitive routing (VPC A cannot talk to VPC C through VPC B).

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

6.  What is AWS Transit Gateway and how is it different from VPC Peering?
    - An AWS Transit Gateway is a central hub that connects multiple VPCs and on-premises networks through a single gateway.
    - Instead of creating many individual peering connections, you can connect all your VPCs to the Transit Gateway, and it automatically handles the routing between them.
    
    Peering vs Transit Gateway:
    - VPC Peering is One-to-one connection between two VPCs while Transit Gateway is a Hub and connecting multiple VPCs through a single gateway.
    - VPC Peering will Not supported for Transitive Routing while Transit Gateway will Supported (A, B, and C can communicate via the TGW).
    - VPC Peering has a limited scalability up to 125 peerings per VPC while Transit Gateway has Highly scalable thousands of VPCs.
    - VPC Peering is used for small or simple network setups while Transit Gateway used for large or enterprise-level multi-VPC architectures.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

7.  How can you connect your on-premises network to AWS VPC?
    - we can connect our on-premises network to AWS VPC in several ways, depending on our use case and requirements
    VPN Connection:
       - A secure, encrypted connection over the internet between your on-premises network and AWS.
       - Easy to set up and good for smaller workloads.

    AWS Direct Connect:
       - A dedicated, private network link between your data center and AWS. Offers higher speed and lower latency than a VPN.

    Transit Gateway:
       - Used when you have multiple VPCs it acts as a central hub to connect your on-premises network and all your VPCs together.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

8. What is a VPN connection in AWS and what are its types?
   - A VPN (Virtual Private Network) connection in AWS is used to securely connect your on-premises network or device to your AWS VPC over the public internet.
   - It creates an encrypted way, ensuring that data travels safely between your data center and AWS.  

   Types of VPN Connections in AWS:
   1.Site-to-Site VPN:
        - Connects your entire on-premises network to your AWS VPC Used for connecting data centers or branch offices to AWS.
        - It consists of a Virtual Private Gateway on AWS and a Customer Gateway on-premises.

   2. Client VPN:
        - Connects individual users or laptops securely to AWS resources.
        - Used for remote users like employees working from home.
        - Fully managed by AWS and supports OpenVPN clients.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

9.  What is AWS Direct Connect and how is it different from VPN?
    - AWS Direct Connect is a dedicated, private network connection between your on-premises data center and AWS.
    - It doesn’t use the public internet instead, it provides a secure, high-speed, low-latency link directly into AWS.
    - It’s ideal for large enterprises or workloads that need consistent network performance and high data transfer speeds.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

10. How do you achieve high availability across multiple Availability Zones?
    - To achieve high availability, we can deploy our application and resources across multiple Availability Zones within a region. This ensures that if one AZ fails, the others can 
      continue to serve traffic without downtime.
    
   Ways to Achieve High Availability Across AZs:
   1. Use Elastic Load Balancer (ELB): Distributes incoming traffic automatically across instances in multiple AZs.
   2. Deploy EC2 Instances in Multiple AZs: Run instances in at least two AZs to prevent a single point of failure.
   3. Use Multi-AZ Databases (RDS, etc): For example, Amazon RDS Multi-AZ automatically replicates data to a standby instance in another AZ.
   4. Use Auto Scaling: Automatically replaces unhealthy instances and launches new ones in healthy AZs.
   5. Store Data in Multi-AZ Services: Services like S3 and DynamoDB automatically replicate data across AZs.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

11. What is the CIDR block in VPC and how do you choose it?
    - A CIDR block (Classless Inter-Domain Routing block) defines the range of IP addresses that your VPC or subnet can use.	
    - For example a CIDR block like 10.0.0.0/16 provides around 65,000 private IP addresses that can be divided into subnets.	
    - When selecting a CIDR block, I make sure to use a private IP range (like 10.0.0.0/8, 172.16.0.0/12, or 192.168.0.0/16) ensure it doesn’t overlap with any on-premises or other VPC
      networks, and choose the size based on how many resources I’ll need.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

12.  How do you expand a VPC’s CIDR range after creation?
     - we can expand a VPC's CIDR range by adding an extra CIDR block through the VPC console or CLI.	
     Steps:
       * Go to the VPC console → select your VPC.
       * Choose “Actions” → “Edit CIDRs”.
       * Click “Add new CIDR block” and enter the new range (for example, add 10.1.0.0/16 to an existing 10.0.0.0/16).
       * Then, you can create new subnets from this new CIDR block.

     Important points:
       * The new CIDR must not overlap with existing VPCs or networks.
       * You can’t shrink or remove the original CIDR only add more.
       * A VPC can have up to 5 IPv4 CIDR blocks can be increased via AWS support.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

13.  How do you restrict access between subnets or VPCs?
     - You can restrict access between subnets or VPCs by using security controls like Security Groups and Network ACLs, and by adjusting route tables.
     Security Groups:
       * Work as virtual firewalls for EC2 instances.
       * You can allow or deny specific inbound/outbound traffic using port, protocol, and IP range.
       * For example, allow traffic only from a specific subnet or security group.

     Network ACLs:
       * Control traffic at the subnet level.
       * You can explicitly allow or deny traffic between subnets using rules based on CIDR blocks, ports, and protocols.

     Route Tables:
       * If we remove or modify routes, you can stop traffic from flowing between subnets or VPCs.
       * Example: Don’t create a route to another subnet or VPC peering connection if you want to restrict access.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

14. What is VPC Flow Logs and how is it used for monitoring? 
    - VPC Flow Logs capture information about the IP traffic going to and from network interfaces in your VPC. They help us to monitor, analyze, and troubleshoot network connections by
      showing which traffic is allowed or denied.
    - How it works:
       * Flow logs can be created at the VPC, Subnet, or Network Interface (ENI) level.
       * The log data includes details like source IP, destination IP, port, protocol, and action (ACCEPT or REJECT).
       * The logs are stored in Amazon CloudWatch Logs or Amazon S3 for analysis.
   - we can use for Monitor network traffic patterns.
   - we can use for Identify security threats or unauthorized access attempts.
   - we can use for Troubleshoot connectivity issues between instances or subnets.
   - we can use for Audit network activity for compliance or investigation.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

15. How do you analyze VPC Flow Logs for security and performance?
    - I analyze VPC Flow Logs by checking for rejected connections, unusual traffic patterns, and high data usage. This helps ensure network security, performance optimization, and 
      troubleshooting within the VPC.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

16. What are endpoints in VPC and what types are available?
    - VPC Endpoints allow us to privately connect VPC to AWS services without using the public internet. This improves security, performance, and reduces data transfer costs, since 
      traffic stays within the AWS network.

    Types of VPC Endpoints:
      * Interface Endpoints:
         - Use Elastic Network Interfaces (ENIs) with private IPs inside your subnet.
         - Support most AWS services (like SSM, CloudWatch, API Gateway, etc.) and many third-party SaaS apps.
         - Communication happens via private IPs, not over the internet.

      * Gateway Endpoints:
         - Used only for Amazon S3 and DynamoDB.
         - Add an entry in the route table that directs traffic to these services privately.
         - No need for NAT Gateway or Internet Gateway for these services.

     * Gateway Load Balancer Endpoints:
        - Used to connect to third-party security appliances like firewalls or inspection tools.
        - Integrates with Gateway Load Balancer for deep packet inspection or traffic filtering.


     Example: we have a VPC with Private Subnet (no internet access) and Public Subnet (with Internet Gateway) EC2 instances in the private subnet we need those EC2s to read/write data 
              from an S3 bucket
 
              - The Problem (Without a VPC Endpoint) EC2 in the private subnet has no Internet Gateway → can’t reach S3.
              - To fix that, we usually add a NAT Gateway (in the public subnet), and Route traffic through it to reach S3 but NAT Gateway adds extra cost Data goes out to the public 
                internet. It’s less secure.

              - The Solution (With a VPC Endpoint)Instead, you create a VPC Gateway Endpoint for S3.
                How it works:
                  - Create a Gateway VPC Endpoint in your VPC.
                  - Attach it to your route tables for the private subnet.
                  - Now, EC2 instances in that subnet can access S3 privately — no internet needed.
                  - The traffic stays inside the AWS network (never leaves to the internet).

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

17. How do you configure Privatelink in a VPC?
    - AWS Privatelink allows private connectivity between VPCs and AWS services without using the public internet.
    - To configure Privatelink, we can create a VPC Endpoint (Interface Endpoint) in our VPC that connects to a service hosted in another VPC or to an AWS service.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

18.  What are the best practices for designing secure VPC architectures?
     - Use separate public and private subnets and public for web servers, private for databases.
     - Apply Security Groups and NACLs allow only required traffic (least privilege).
     - Use VPC Endpoints access AWS services like S3 privately without internet.
     - Enable VPC Flow Logs and CloudTrail monitor and log all activity.
     - Encrypt data use KMS for data at rest and TLS for data in transit.
     - Limit public IPs – only give internet access where necessary.
     - Use Transit Gateway or VPN for secure hybrid or multi-VPC connections.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

19. How can you create a multi-region VPC setup?
    - we can’t stretch a single VPC across regions, but you can create separate VPCs in different AWS regions and then connect them securely.
    - connected via VPC Peering, Transit Gateway, or PrivateLink, with Route 53 handling traffic routing and data replication for resilience.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

20. What are common VPC misconfigurations and how to troubleshoot them?
    - Common VPC misconfigurations usually involve routing, permissions, or connectivity issues.

    1. Incorrect Route Table Configuration
        - Issue: Instances in private subnets can’t reach the internet.
        - Cause: Missing route to NAT Gateway or Internet Gateway.
        - Fix: Check the route table for correct routes and subnet associations.

    2. Security Group or NACL Blocking Traffic
        - Issue: Instances can’t communicate with each other or external services.
        - Cause: Inbound/outbound rules not allowing the required port or CIDR.
        - Fix: Review security groups and NACLs — ensure proper allow rules.

    3. Overlapping CIDR Blocks
        - Issue: VPC peering or VPN connection fails.
        - Cause: Two VPCs or networks have overlapping IP ranges.
        - Fix: Redesign one VPC’s CIDR range to avoid overlap.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

21. How do you automate VPC creation using Terraform or CloudFormation?

22. How do you integrate VPC with AWS Load Balancer and Auto Scaling?
