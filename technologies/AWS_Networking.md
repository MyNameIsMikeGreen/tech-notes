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

# Referencing Instances
## General
If we have an application with instances across AZs we need to be able to distribute the work across these instances. For this, we use a load balancer.

If we have an application with instances across regions, we need to setup DNS entries to identify each region and direct traffic to a particular one. In AWS, the DNS manager is known as **Route 53**. Each region may still have multiple AZs and will also have a load balancer inside.

## Internal IP Addresses

### Subnet Masks and CIDR Notation
Each subnet must be created with a specification of which IP addresses that subnet manages. In the old days, this would be noted using an IP address and a subnet mask. However, more commonly, especially when using AWS, the CIDR notation is used. For example, an IP address of 131.10.55.70 with a subnet mask of 255.0.0.0 (which has 8 network bits) would be represented as 131.10.55.70/8 in CIDR notation ([Source](http://www.controltechnology.com/Files/common-documents/application_notes/CIDR-Notation-Tutorial)). What this means in practice is that all the addresses in this particular subnet are in the form 131.x.x.x.

### AWS IP Address Ranges

AWS subnets can be specified as being between /16 and /28 inclusive. However, the first 4 and final 4 addresses in that range are reserved by AWS and are not usable by the customer.

CIDR ranges must not overlap between subnets in the same VPC. Each subnet manages a mutually exclusive set of IP addresses.

## External IP Addresses

An instance is not automatically given a public IP address. One must be explicitly requested in the instance configuration.

By default, if an instance is deleted, it will lose its public IP address. When recreated, it will lose the IP address it previously had. To maintain a static IP address, we can use an *elastic IP address*. Elastic IP addresses are assigned to an instance, and will remain whether the instance is destroyed or created. This IP address can also be migrated over to a new instance if desired. This, in effect, is a mechanism for reserving an IP address for your exclusive use.

# Access Control

## Security Groups

Security groups are policies which determine who/what can connect to an instance. The policy works on the whitelist model *(CONFIRM THIS)* and is made up of:
* The instance to protect (destination)
* The source of the request
* The protocol
* The port

## Network Access Control Lists (NACLs)

A policy defining who/what is allowed to send/receive traffic for a particular subnet. It is similar to security groups, operating on subnets rather than instances. One NACL may be applied to multiple subnets at once.

## Peered VPCs

VPCs can be defined as *peers* of each other. Peered VPCs act as if they were part of the same VPC.

Peer VPCs can be anywhere regardless of AWS or region.

## VPNs

A VPN can be set up to connect to a VPC from an external network. This allows communication to instances as if the source were inside the VPC.

## Internet Gateways

Internet gateways are used to provide access to a VPC from the public internet.

## NAT Gateway

Similar to internet gateways, these provide access to the public internet in a VPC but instead for private subnets.

# Resources
* http://www.controltechnology.com/Files/common-documents/application_notes/CIDR-Notation-Tutorial
