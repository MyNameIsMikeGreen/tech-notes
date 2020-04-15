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
Each subnet must be created with a specification of which IP addresses that subnet manages. In the old days, this would be noted using an IP address and a subnet mask. However, more commonly, especially when using AWS, the CIDR notation is used. For example, an IP address of `131.10.55.70` with a subnet mask of `255.0.0.0` (which has 8 network bits) would be represented as `131.10.55.70/8` in CIDR notation ([Source](http://www.controltechnology.com/Files/common-documents/application_notes/CIDR-Notation-Tutorial)). What this means in practice is that all the addresses in this particular subnet are in the form `131.x.x.x`.

### AWS IP Address Ranges

AWS subnets can be specified as being between /16 and /28 inclusive. However, the first 4 and final 4 addresses in that range are reserved by AWS and are not usable by the customer.

CIDR ranges must not overlap between subnets in the same VPC. Each subnet manages a mutually exclusive set of IP addresses.

## External IP Addresses

An instance is not automatically given a public IP address. One must be explicitly requested in the instance configuration.

By default, if an instance is deleted, it will lose its public IP address. When recreated, it will lose the IP address it previously had. To maintain a static IP address, we can use an *elastic IP address*. Elastic IP addresses are assigned to an instance, and will remain whether the instance is destroyed or created. This IP address can also be migrated over to a new instance if desired. This, in effect, is a mechanism for reserving an IP address for your exclusive use.

## DNS

AWS has its own DNS service known as *Route 53*. It is a fairly typical DNS service which allows a customer to register domain names and point these domains to an IP address.

Route 53, being an Amazon product, can integration with many other AWS services. Instead of the DNS entry pointing simply to a static IP address, it can point to various locations in AWS such as:
  * Load balancers
  * EC2 instances
  * S3 buckets
  * API Gateways

# Access Control

## Technologies

### Security Groups

Security groups are policies which determine who/what can connect to an instance. The policy works on the whitelist model *(CONFIRM THIS)* and is made up of:
* The instance to protect (destination)
* The source of the request
* The protocol
* The port

### Network Access Control Lists (NACLs)

A policy defining who/what is allowed to send/receive traffic for a particular subnet. It is similar to security groups, operating on subnets rather than instances. One NACL may be applied to multiple subnets at once.

### Peered VPCs

VPCs can be defined as *peers* of each other. Peered VPCs act as if they were part of the same VPC.

Peer VPCs can be anywhere regardless of AWS or region.

Peering is not transitive. An explicit peering must be established between any two VPCs which wish to communicate with each other, they cannot use a common peer to do so.

### VPNs

A VPN can be set up to connect to a VPC from an external network. This allows communication to instances as if the source were inside the VPC.

### Route Tables / Routers

Route tables define how to route traffic that is coming into or out of subnets.

### Internet Gateways

Internet gateways are used to provide access to a VPC from the public internet.

### NAT Gateways

NAT gateways sit inside a public subnet and offer instances within a private subnet the ability to make a request and receive a response out to the public internet.

### Bastion / Jump Box

These are special EC2 instances which act as a proxy for traffic to instances in private subnets which are initiated from the public internet. At a high level, NAT Gateways and Bastions do the same thing, but offer a different end to initiate the request.

## Allowing Access to/from the Internet for a Private Instance

### Bastions and NAT Gateways

A private subnet only has internal IP addresses assigned to its instances. Therefore, it is not possible to directly connect to them from the public internet. However, it may be desirable to connect to the instances from the internet in some circumstances. In order to do this, you can use a *bastion* (Also known as a *jump box*) to proxy your requests.

A bastion instance is set up in a public subnet and and internet gateway set up on the VPC. Through the internet gateway, an external entity can connect to the bastion as it would any other instance inside a public subnet - via the internet gateway. The bastion instance would then connect to the target instance on the external source's behalf using typical subnet-to-subnet methods such as a routing table.

A limitation of a bastion is that it only supports requests which have been initiated from outside the VPC. It does not provide the private instance with the ability to initiate requests to the public internet. To accomplish this, a NAT gateway can be instantiated in the public subnet. This acts similarly to the bastion, but handles traffic initiated by the private instance. Through this, the private instance can perform actions such as updating software.

### VPNs

If setting up and maintaining a bastion instance is not desirable, it is possible to manage a private instance via a VPN. To do this, a *VPN Gateway* is set up on the VPC in a similar fashion as an *Internet Gateway* would be. On the client-side, a *Customer Gateway* would be set up. The two gateways would then communicate with each other via a VPN connection. The VPN gateway would be communicating to the private instance via the routing table in a similar way as the bastion would be in the other scenario.

# Load Balancing
It may not be appropriate for one entity to handle all the traffic for a system. Therefore, AWS's *Elastic Load Balancing* (ELB) can distribute traffic amongst the appropriate:
* EC2 Instances
* Lambdas
* IP Addresses
* Containers
* On-Premises Resources

A load balancer can be one of two types:
* Application load balancer
  * Operates at level 7 (application layer) of the OSI model
  * As it acts at the HTTP/HTTPS level, authentication management can be offloaded to the load balancer to reduce computing pressure on applications. It also centralises certificate management at the load balancer level, easing management concerns.
  * This means that authentication and other overhead operations can be handled by the load balancer rather than the application if desired.
* Network load balancer
  * Operates at level 4 (transport layer) of the OSI model.
  * Suitable for very high throughput and long-lived connection protocols such as TCP.

# API Gateway

AWS's *API Gateway* is a standalone system for accepting HTTP/REST and WebSocket requests, extracting any variables/headers/bodies and forwarding these onto some application or service which doesn't have the ability to accept these requests itself. From a Java developer's perspective, it is like Amazon have extracted the controller layer of a Spring application into a service.

It also has the capability to transform any response the application/service provides as a response before returning it to the client.

## Authentication

API Gateway supports several authentication methods:
* Amazon Cognito
  * For integration with external identity providers such as Google and Facebook.
* AWS Identity and Access Management (AIM)
* Lambda Authorisers
  * A Lambda function which determines authorisation
  * Commonly used for OAuth, SAML, or JWT.

## Additional Features
* Throttling
  * Request rates can be limited in various ways. Such as by total amount of concurrent requests or rate of requests in a specific time frame.
  * Users of the gateway can be tracked by the use of API keys and HTTP 429 responses returned if the user is requesting an endpoint too frequently.
* Release management
  * Support for development stages (e.g. dev, stg, prd)
  * Canary releases can be used to direct a certain proportion of the traffic to certain development stages. This is useful in testing out new features.
* Response mocking
  * If the end service has not been developed yet, API Gateway can mock the response of this service and return it back to the client.
* SDK generation
  * Once an API has been setup in API Gateway, there is a tool available to generate an SDK in a variety of languages which can call the API Gateway endpoint.
* Documentation generation
  * Once an API has been setup in API Gateway, there is a tool available to generate documentation for the API in various formats including Swagger.

# Resources
* https://app.pluralsight.com/player?course=aws-network-design-getting-started
* http://www.controltechnology.com/Files/common-documents/application_notes/CIDR-Notation-Tutorial
