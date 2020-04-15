AWS Networking
==============

# Instance Locations
* Regions
  * The global AWS network is divided up into regions. These are like self-contained AWS instances which a customer can choose to host their services on.
  * Regions named and physically located geographically. Examples include: *Ireland*, *London*, and *Beijing*.
  * Not all regions are equal and care should be taken to choose the most appropriate one for the customer. Regions can differ on:
    * **Performance** - Regions geographically close to the end-user generally offer the best performance.
    * **Services available** - New services are generally trialed in a select few regions before rolled out to all regions.
    * **Price of services** - The same service may cost different amounts across different regions.
* Availability Zones (AZs)
  * Each region is divided up into at least 2 AZs.
  * AZs are physically located in different locations within a geographical region.
  * AZs within the same region are connected by proprietary high-speed networks to enable service replicas to be kept up to date across regions. These networks do not utilise the public internet.
  * If one AZ suffers downtime, in theory, another AZ will be able to take over the load.
* Virtual Private Clouds (VPCs)
  * A VPC is a customer's own private area of a region.
  * A VPC spans across the same AZs as the region it is a part of.
  * The network of a VPC can be divided further into subnets to isolate certain service instances from one another within the same VPC.
  * By default, every AWS account has a default VPC set up automatically that is ready to use without configuration. Further VPCs can be configured as desired.

# Networking
If we have an application with instances across AZs we need to be able to distribute the work across these instances. For this, we use a load balancer.

If we have an application with instances across regions, we need to setup DNS entries to identify each region and direct traffic to a particular one. In AWS, the DNS manager is known as **Route 53**. Each region may still have multiple AZs and will also have a load balancer inside.
