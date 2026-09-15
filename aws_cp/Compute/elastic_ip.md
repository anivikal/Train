Elastic IP (EIP) in AWS is a static public IPv4 address that you allocate to your AWS account and associate with a resource, usually an EC2 instance or a network interface.

Normally, when an EC2 instance receives a public IPv4 address, that address can change when the instance is stopped and started. An EIP lets you keep the same public IPv4 address even if the underlying instance changes.

Why an EIP is useful

Suppose your application is accessed at:

http://203.0.113.25

Without an EIP:

1. Your EC2 instance gets a public IP.
2. You stop and start the instance.
3. AWS may assign a different public IP.
4. DNS records, firewall rules, allowlists, or clients using the old IP may stop working.

With an EIP:

1. You allocate  203.0.113.25  to your AWS account.
2. You associate it with an EC2 instance or network interface.
3. You can stop/start or replace the instance.
4. You re-associate the same EIP with another resource.

The address remains under your control until you release it.

Important characteristics

Static public IPv4 address

An EIP is reachable over the public internet, subject to:

• Security group rules
• Network ACLs
• Route tables
• Operating-system firewalls
• The service listening on the instance

It is not a private VPC address such as:

10.0.1.25

Instead, it is a public IPv4 address mapped to a private address inside your VPC.

Associated with a network interface

For EC2, the EIP is effectively associated with an Elastic Network Interface (ENI). You can associate it with:

• An EC2 instance
• A specific ENI
• A NAT gateway
• Some other AWS resources that support EIPs

An EC2 instance can have multiple network interfaces and, depending on configuration and limits, multiple public IPv4 addresses.

Can be remapped

You can disassociate an EIP from one resource and associate it with another. This is useful for failover:

EIP -> Primary EC2 instance

If the primary fails:

EIP -> Standby EC2 instance

Clients continue using the same public IP.

There may be a short interruption while the reassociation takes place, so EIPs are not a complete substitute for highly available architectures.

EIP lifecycle

The normal lifecycle is:

Allocate -> Associate -> Use -> Disassociate -> Release

1. Allocate

You request an EIP from AWS:

aws ec2 allocate-address --domain vpc

AWS assigns an IPv4 address to your account.

2. Associate

You attach it to an EC2 instance or ENI:

aws ec2 associate-address \
  --allocation-id eipalloc-0123456789abcdef0 \
  --instance-id i-0123456789abcdef0

3. Disassociate

You detach it from the resource:

aws ec2 disassociate-address \
  --association-id eipassoc-0123456789abcdef0

4. Release

When you no longer need it, release it:

aws ec2 release-address \
  --allocation-id eipalloc-0123456789abcdef0

Once released, you generally cannot get the exact same IP back.

EIP versus ordinary public IPv4 address

┌──────────────────────────────┬──────────────────────────────────┬──────────────────────┐
│ Feature                      │ Automatically assigned public IP │ Elastic IP           │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Static across stop/start     │ No                               │ Yes                  │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Owned by your account        │ No                               │ Yes, while allocated │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Can move to another instance │ Generally no                     │ Yes                  │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Requires explicit allocation │ No                               │ Yes                  │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Suitable for IP allowlists   │ Risky                            │ Yes                  │
├──────────────────────────────┼──────────────────────────────────┼──────────────────────┤
│ Public IPv4 address          │ Yes                              │ Yes                  │
└──────────────────────────────┴──────────────────────────────────┴──────────────────────┘

An automatically assigned public IP is usually released when the instance is stopped or terminated. An EIP remains allocated until you explicitly release it.

EIP versus private IP

An EC2 instance commonly has:

Private IP: 10.0.1.25
Public/EIP: 203.0.113.25

The private IP is used inside the VPC. The EIP is used for internet communication. AWS performs address translation between the public and private sides.

Private IPs are usually preferred for communication between AWS resources because they avoid unnecessary internet routing and are generally more stable within the VPC.

Common use cases

Hosting a public server

A traditional web server, API, VPN server, mail server, or bastion host may need a stable public IP.

IP allowlisting

External systems may allow requests only from known source IPs. An EIP gives your application a stable address to provide to those systems.

Failover

You can move an EIP from a failed instance to a standby instance.

NAT gateway

A public NAT gateway uses an EIP so that private-subnet instances can access the internet using a predictable source IP.

For example:

Private EC2 instances
        |
        v
NAT Gateway + EIP
        |
        v
Internet

External services then see the NAT gateway's EIP rather than the private instances' addresses.

EIP and DNS

You can point a DNS record to an EIP:

api.example.com -> 203.0.113.25

However, for most production applications, it is usually better to point DNS to a managed endpoint such as:

• Application Load Balancer
• Network Load Balancer
• CloudFront
• API Gateway
• Route 53 alias target

This avoids tightly coupling your DNS configuration to one EC2 instance.

Costs and quotas

AWS charges for public IPv4 usage, including Elastic IPs. The exact pricing depends on the AWS region and current AWS pricing policy.

Important practical points:

• An EIP that is allocated but not being used can incur charges.
• Public IPv4 addresses attached to resources may also incur charges.
• NAT gateways have separate hourly and data-processing charges.
• EIPs are subject to regional account quotas.
• AWS generally limits the number of EIPs you can allocate by default.
• You can request a quota increase, but AWS may require justification.

You can check EIP-related quotas in the Service Quotas console or with the AWS CLI.

Security considerations

An EIP itself does not make an instance secure or insecure. Security depends on your configuration.

For a public EC2 instance, review:

• Security groups
• Network ACLs
• SSH access restrictions
• Operating-system firewall rules
• Patch management
• IAM permissions
• Whether the service should be public at all

For example, allowing SSH from everywhere is risky:

0.0.0.0/0 on TCP port 22

Prefer restricting access to trusted corporate or administrator IP ranges.

EIP limitations

An EIP is not a complete high-availability solution:

• It is IPv4-only.
• It represents a single public endpoint.
• Moving it between instances can cause a brief interruption.
• It does not automatically distribute traffic.
• It does not replace backups or replication.
• It does not automatically detect failures and perform failover.

For higher availability, use architectures such as:

Route 53 + Load Balancer + Multiple EC2 instances

or:

CloudFront + Application Load Balancer + Auto Scaling

When not to use an EIP

Avoid assigning an EIP directly to every server when you can use a managed service instead. For example:

• Use an Application Load Balancer for HTTP/HTTPS applications.
• Use CloudFront for globally distributed content.
• Use Route 53 health checks for DNS-based failover.
• Use private IPs for internal service-to-service communication.
• Use IPv6 where supported to reduce dependence on public IPv4 addresses.

Simple example

You run a web server on EC2:

EC2 private IP: 10.0.1.20
Temporary public IP: 198.51.100.10

You stop and start the instance, and the public IP changes:

New public IP: 198.51.100.44

Any DNS record or firewall rule referencing  198.51.100.10  may stop working.

With an EIP:

EIP: 198.51.100.50
EC2 private IP: 10.0.1.20

After a stop/start, the private address may remain associated with the ENI and the EIP remains:

EIP: 198.51.100.50

If you replace the instance, you can associate the same EIP with the replacement.

In short: an AWS Elastic IP is a reserved, static public IPv4 address that you control and can move between supported AWS resources. It is useful when a stable public IP is required, but for scalable production systems, load balancers, DNS, CloudFront, and managed AWS endpoints are often better choices.
