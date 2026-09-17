Part 1: Amazon EC2

1. What is EC2?

Amazon EC2 (Elastic Compute Cloud) provides virtual servers in AWS.

You choose:

• CPU
• Memory
• Storage
• Network speed
• Operating system
• GPU or special hardware
• Availability Zone
• Payment model

An EC2 instance is defined by:

Instance family + generation + size

Example:

m7i.2xlarge

┌─────────┬────────────────────────┐
│ Part    │ Meaning                │
├─────────┼────────────────────────┤
│ m       │ General-purpose family │
├─────────┼────────────────────────┤
│ 7       │ Generation             │
├─────────┼────────────────────────┤
│ i       │ Intel processor        │
├─────────┼────────────────────────┤
│ 2xlarge │ Instance size          │
└─────────┴────────────────────────┘

Example:

c7g.large

┌───────┬────────────────────────┐
│ Part  │ Meaning                │
├───────┼────────────────────────┤
│ c     │ Compute optimized      │
├───────┼────────────────────────┤
│ 7     │ Generation             │
├───────┼────────────────────────┤
│ g     │ AWS Graviton processor │
├───────┼────────────────────────┤
│ large │ Instance size          │
└───────┴────────────────────────┘

────────────────────

2. EC2 instance naming system

AWS instance names often use letters that indicate the design.

┌────────┬────────────────────────────────────┐
│ Suffix │ Meaning                            │
├────────┼────────────────────────────────────┤
│ a      │ AMD processor                      │
├────────┼────────────────────────────────────┤
│ g      │ AWS Graviton processor             │
├────────┼────────────────────────────────────┤
│ i      │ Intel processor                    │
├────────┼────────────────────────────────────┤
│ n      │ Enhanced networking                │
├────────┼────────────────────────────────────┤
│ d      │ Local NVMe instance storage        │
├────────┼────────────────────────────────────┤
│ e      │ More memory than the normal family │
├────────┼────────────────────────────────────┤
│ b      │ Higher network bandwidth           │
├────────┼────────────────────────────────────┤
│ z      │ High CPU frequency                 │
├────────┼────────────────────────────────────┤
│ flex   │ Flexible CPU configuration         │
├────────┼────────────────────────────────────┤
│ metal  │ Bare-metal server                  │
└────────┴────────────────────────────────────┘

For example:

r7gd.4xlarge

means approximately:

•  r : memory optimized
•  7 : seventh-generation design
•  g : Graviton CPU
•  d : local NVMe disks
•  4xlarge : size

AWS changes available families frequently, so focus on the family purpose, not memorizing every model.

────────────────────

3. EC2 instance families

A. General-purpose instances

Main families

•  T 
•  M 
•  A 

These are used when the workload needs a balanced amount of CPU, memory, and network performance.

────────────────────

T family: Burstable performance

Examples:

t3.micro
t3a.medium
t4g.small

How T instances work

T instances have:

1. A baseline CPU performance
2. A bank of CPU credits
3. The ability to temporarily use more CPU

When the instance uses less CPU than its baseline, it earns credits. When it uses more CPU, it spends credits.

Good for

• Small web servers
• Development environments
• Low-traffic websites
• Small databases
• Microservices
• Testing
• Administrative tools

Not ideal for

• Continuous CPU-heavy processing
• Video encoding
• Scientific simulations
• Large-scale data processing

Cost-saving advantage

T instances are often cheaper than fixed-performance instances for workloads with irregular CPU usage.

Important warning

A burstable instance may become expensive or slow when it continuously uses high CPU. Monitor:

•  CPUCreditBalance 
•  CPUSurplusCreditBalance 
•  CPUSurplusCreditsCharged 

T instances can operate in:

• Standard mode: performance is limited when credits finish
• Unlimited mode: performance continues, but surplus credits may incur charges

Use Unlimited mode only when high CPU usage is unusual and short-lived.

────────────────────

M family: Balanced general purpose

Examples:

m7i.large
m7g.xlarge
m8g.large

Good for

• Application servers
• Backend systems
• Medium databases
• Enterprise software
• Java applications
• Business systems
• Container workloads

M instances provide a balanced relationship between:

• CPU
• Memory
• Network
• EBS throughput

Cost principle

If you do not know whether you need compute optimization or memory optimization, M is a good starting point.

────────────────────

A family: AMD general purpose

Examples:

a1.medium
a1.large

A instances use AMD processors and may provide lower prices than equivalent Intel instances.

Good for

• General-purpose applications
• Web servers
• Linux workloads
• Applications compatible with x86 AMD processors

Cost principle

AMD instances are often less expensive than similar Intel instances. Test application performance before changing production systems.

────────────────────

B. Compute-optimized instances

C family

Examples:

c7i.large
c7g.xlarge
c7a.2xlarge

C instances provide more CPU relative to memory.

Good for

• High-performance web servers
• Batch processing
• Video encoding
• Scientific computation
• Gaming servers
• High-performance APIs
• Machine learning inference
• Distributed analytics
• CPU-based financial calculations

Typical design

Compared with M instances:

• More vCPUs per GB of memory
• Higher CPU performance
• Lower memory ratio

Cost principle

C instances are cheaper than M instances when the workload is CPU-bound. Using a memory-heavy instance for a CPU-heavy workload wastes money.

────────────────────

Hpc family

Examples may include:

hpc6a
hpc7g
hpc7a

These are designed for high-performance computing.

Good for

• Computational fluid dynamics
• Weather simulation
• Molecular research
• Engineering models
• Seismic analysis
• Large scientific calculations

Features

• High CPU performance
• High network bandwidth
• Low-latency communication
• Support for tightly coupled workloads

Cost principle

HPC instances are expensive per hour but can be cheaper per completed scientific job because they finish work faster.

The correct metric is often:

Cost per completed job

not merely:

Cost per instance-hour

────────────────────

C. Memory-optimized instances

Memory-intensive workloads are limited by RAM rather than CPU.

R family: Memory optimized

Examples:

r7i.large
r7g.xlarge
r7a.2xlarge

Good for

• Relational databases
• In-memory caches
• Data analytics
• Real-time big-data processing
• Search engines
• SAP systems
• Large JVM applications

R instances usually have more memory per vCPU than M instances.

────────────────────

X family: Extremely memory optimized

Examples:

x2idn
x2iedn
x8g

Good for

• Very large in-memory databases
• SAP HANA
• Large Redis clusters
• Large-scale analytics
• Scientific memory-intensive applications

Cost principle

They are expensive, but a smaller number of large-memory instances may cost less than many smaller instances when the application requires a large shared memory space.

────────────────────

U family: High-memory instances

Examples:

u-6tb1
u-9tb1
u-12tb1

These provide terabytes of RAM.

Good for

• Very large SAP HANA systems
• Large in-memory databases
• Enterprise analytics
• Systems that cannot partition data easily

Cost caution

These instances are extremely expensive. They should be used only when memory requirements genuinely require them.

────────────────────

Z family: High-memory and high-frequency CPU

Examples:

z1d

Z instances provide:

• Large memory
• High CPU clock speed
• Low-latency processing

Good for

• Electronic design automation
• Financial applications
• Per-core licensing systems
• High-speed relational databases
• Applications sensitive to CPU frequency

────────────────────

D. Storage-optimized instances

I family

Examples:

i4i
i7i
i8g

I instances provide high-speed local NVMe SSD storage.

Good for

• NoSQL databases
• Elasticsearch/OpenSearch
• Log processing
• Data warehouses
• Distributed file systems
• Temporary high-speed data processing

Important property

Local instance storage is generally ephemeral. Data may be lost if:

• The instance is stopped
• The instance is terminated
• Hardware fails

Use S3, EBS, backups, or replication for durable data.

────────────────────

D family

Examples:

d3
d3en

D instances provide large local storage capacity.

Good for

• Distributed file systems
• Hadoop
• Data lakes
• Log storage
• Large-scale sequential workloads

They usually prioritize storage capacity over extremely high IOPS.

────────────────────

H family

Examples:

h1

H instances provide large HDD-based local storage.

Good for

• MapReduce
• Distributed file systems
• Data warehouses
• Large sequential reads and writes

They are not ideal for random-access workloads that require very low latency.

────────────────────

E. Accelerated-computing instances

These instances include GPUs, FPGAs, or specialized accelerators.

P family: GPU computing and machine learning training

Examples:

p4
p5
p6

Good for

• Deep learning training
• Large language models
• Scientific GPU workloads
• High-performance computing
• Image and video processing

They are among the most expensive EC2 instances.

Cost-saving methods

• Use Spot Instances for fault-tolerant training
• Save and reuse checkpoints in S3
• Stop idle training instances
• Use smaller GPU instances for experimentation
• Use managed training services when utilization is low
• Optimize batch size and GPU utilization

A costly GPU with only 20% utilization is usually a design problem.

────────────────────

G family: Graphics and ML inference

Examples:

g4dn
g5
g6

Good for

• Graphics rendering
• Game streaming
• Video processing
• Computer vision
• Machine learning inference
• Virtual workstations

Compared with P instances, G instances are often more suitable for inference and graphics workloads.

────────────────────

Inf family: AWS Inferentia

Examples:

inf1
inf2

These use AWS Inferentia chips for machine learning inference.

Good for

• Neural-network inference
• Image classification
• Recommendation systems
• Language-model serving

Cost principle

Inferentia may provide a lower cost per inference than general-purpose GPU instances, but the model must be compatible with the supported software stack.

────────────────────

Trn family: AWS Trainium

Examples:

trn1
trn2

These are designed for machine learning training.

Cost principle

Trainium can reduce training cost when the workload is adapted to AWS Neuron software.

────────────────────

F family: FPGA

Examples:

f1

F instances contain FPGAs.

Good for

• Custom hardware acceleration
• Genomics
• Financial calculations
• Network processing
• Image processing

They require specialized FPGA development knowledge.

────────────────────

DL family: Deep learning systems

Examples:

dl1
dl2q

These are specialized for deep learning training and inference, often using accelerator hardware.

────────────────────

F. Specialized EC2 types

Bare metal

Examples:

.metal

Bare-metal instances give direct access to the physical server.

Good for

• Hypervisor development
• Licensing systems
• Applications requiring direct hardware access
• Performance-sensitive workloads
• Specialized virtualization

Disadvantage

They may reduce virtualization flexibility and can be expensive.

────────────────────

High-performance networking instances

Some instances provide:

• Enhanced networking
• Elastic Fabric Adapter, or EFA
• Very high network bandwidth
• Low-latency packet processing

Good for

• HPC
• Distributed machine learning
• High-frequency systems
• Large-scale distributed databases

────────────────────

Previous-generation instances

AWS may retain older instances for compatibility.

They can sometimes be cheaper, but they may have:

• Lower performance
• Lower energy efficiency
• Fewer features
• Limited availability

Do not select them automatically just because the hourly price is lower. Compare:

Total cost = hourly cost × execution time

────────────────────

4. CPU architecture choices

x86

Provided by:

• Intel
• AMD

Advantages:

• Broad software compatibility
• Mature commercial software support
• Easy migration from traditional servers

Disadvantages:

• May cost more
• May consume more energy
• Not always the best price-performance option

────────────────────

AWS Graviton

Provided by AWS-designed Arm processors.

Examples:

t4g
m7g
c7g
r7g

Advantages:

• Often lower price
• Good performance per dollar
• Lower energy consumption
• Suitable for Linux, containers, Java, Python, Go, and many cloud-native applications

Potential issues:

• Some x86-only binaries may not work
• Some commercial software licenses may not support Arm
• Native libraries may need recompilation
• Testing is required

Cost-saving recommendation

For compatible Linux or container workloads, test Graviton before choosing Intel or AMD.

────────────────────

5. EC2 pricing models

5.1 On-Demand Instances

You pay for actual usage without a long-term commitment.

Good for

• New applications
• Unpredictable workloads
• Short-term projects
• Testing
• Disaster recovery
• Applications that cannot tolerate interruption

Advantages

• No commitment
• Maximum flexibility
• Easy capacity changes

Disadvantage

• Usually the highest long-term price

────────────────────

5.2 Reserved Instances

You commit to a specific usage pattern for one or three years.

Types include:

Standard Reserved Instance

• Largest discount
• Less flexibility
• Suitable for stable workloads

Convertible Reserved Instance

• Can change instance family, operating system, or size within rules
• More flexible
• Usually a smaller discount than Standard RI

Payment options

• All Upfront
• Partial Upfront
• No Upfront

The effective hourly rate generally decreases as more money is paid upfront.

Example calculation

Suppose:

On-Demand annual cost = $10,000
Reserved Instance annual effective cost = $6,500

Savings:

$10,000 - $6,500 = $3,500

Savings percentage:

($3,500 / $10,000) × 100 = 35%

────────────────────

5.3 Savings Plans

Savings Plans provide a discount in exchange for a committed amount of spending per hour for one or three years.

Compute Savings Plans

More flexible. They can generally apply across:

• EC2 instance families
• Regions
• Operating systems
• Instance sizes
• Some compute services

EC2 Instance Savings Plans

Larger discount but less flexibility. They are tied more closely to:

• Instance family
• Region

Difference between Reserved Instances and Savings Plans

┌─────────────┬───────────────────────────────────────┬───────────────────────────────────────────────┐
│ Feature     │ Reserved Instance                     │ Savings Plan                                  │
├─────────────┼───────────────────────────────────────┼───────────────────────────────────────────────┤
│ Commitment  │ Instance configuration or reservation │ Spending commitment per hour                  │
├─────────────┼───────────────────────────────────────┼───────────────────────────────────────────────┤
│ Flexibility │ Lower, especially Standard RI         │ Usually higher                                │
├─────────────┼───────────────────────────────────────┼───────────────────────────────────────────────┤
│ Best for    │ Stable, predictable instance usage    │ Organizations expecting configuration changes │
├─────────────┼───────────────────────────────────────┼───────────────────────────────────────────────┤
│ Payment     │ Upfront, partial, or none             │ Upfront, partial, or none                     │
└─────────────┴───────────────────────────────────────┴───────────────────────────────────────────────┘

────────────────────

5.4 Spot Instances

Spot Instances use unused AWS capacity.

They can be dramatically cheaper than On-Demand Instances, but AWS may interrupt them.

Good for

• Batch processing
• Big-data processing
• CI/CD workers
• Stateless web servers
• Container workers
• Machine learning training
• Rendering
• Simulations
• Parallel jobs

Not good for

• Single-instance databases
• Critical stateful systems
• Workloads without checkpointing
• Systems that cannot tolerate interruption

Spot design principle

A Spot workload should be:

Checkpointable + retryable + distributed

Use:

• Auto Scaling groups
• Multiple instance types
• Multiple Availability Zones
• Checkpoints in S3
• Queue-based processing
• Automatic interruption handling

────────────────────

5.5 Dedicated Hosts

You rent a physical server dedicated to your account.

Good for

• Per-core software licensing
• Compliance requirements
• Server-bound licenses
• Visibility into physical hardware

Cost

Usually more expensive than normal shared-tenancy EC2.

────────────────────

5.6 Dedicated Instances

Instances run on hardware dedicated to one AWS customer, but you do not necessarily control the physical host.

Good for

• Certain compliance requirements
• Workloads that cannot use shared tenancy

────────────────────

5.7 Capacity Reservations

Capacity Reservations reserve EC2 capacity in a particular Availability Zone.

They are useful when you need guaranteed capacity, but they may create cost if the reserved capacity is not used.

────────────────────

6. EC2 cost-saving methods

6.1 Right-sizing

Right-sizing means selecting an instance whose resources match actual usage.

Do not choose based only on maximum possible traffic.

Review:

• CPU utilization
• Memory utilization
• Network throughput
• EBS throughput
• Disk I/O
• Load-balancer metrics
• Application latency
• Queue depth

Example

If an instance has:

CPU: 12%
Memory: 35%
Network: low

It may be oversized.

However, do not downsize based only on CPU. A database might have low CPU but high memory requirements.

────────────────────

6.2 Use Auto Scaling

Auto Scaling adjusts the number of instances according to demand.

Without Auto Scaling:

10 instances × 24 hours × 30 days

With Auto Scaling:

2 instances at night
10 instances during peak hours

This reduces the cost of idle capacity.

Use:

• Target tracking
• Scheduled scaling
• Predictive scaling
• Step scaling
• Queue-length scaling

────────────────────

6.3 Use the right architecture

A traditional design may use one large server. A better design may use:

• Load balancer
• Multiple smaller instances
• Auto Scaling
• Queue
• Managed database
• Object storage

This improves availability and can reduce wasted capacity.

────────────────────

6.4 Shut down non-production systems

Development and test servers often run continuously even when nobody is using them.

Use:

• Instance Scheduler
• AWS Systems Manager
• EventBridge
• Lambda
• Automation runbooks

Example schedule:

Start: 08:00 on weekdays
Stop: 19:00 on weekdays

A server running 11 hours instead of 24 hours can reduce compute runtime by approximately:

1 - (11 / 24) = 54.2%

This excludes storage, IP, and other charges.

────────────────────

6.5 Use Graviton where possible

Test applications on Arm-based Graviton instances.

Potential benefits:

• Lower instance cost
• Better performance per dollar
• Lower energy use

Validate:

• Container images
• Native dependencies
• Database drivers
• Monitoring agents
• Commercial libraries

────────────────────

6.6 Use Spot for interruptible work

Spot is one of the strongest EC2 cost-saving tools.

Design the application to tolerate:

• Termination
• Rebalancing
• Capacity changes
• Delayed jobs

Never place the only copy of important data on a Spot instance.

────────────────────

6.7 Use Savings Plans for stable baseline usage

First identify the minimum continuous usage.

Example:

Normal baseline: 20 instances
Peak usage: 100 instances

Commit only for the stable baseline:

Savings Plan coverage: approximately 20-instance equivalent
Peak usage: On-Demand or Spot

This avoids overcommitting.

────────────────────

6.8 Use RI or Savings Plan utilization reports

Track:

• Coverage
• Utilization
• Unused commitment
• Expiry date
• Monthly recurring cost

A discount is not a saving if the commitment is unused.

────────────────────

6.9 Avoid unnecessary data transfer

EC2 costs can include:

• Internet data transfer
• Cross-Region transfer
• Cross-AZ transfer
• NAT Gateway processing
• Load-balancer processing

Reduce cost by:

• Keeping communicating resources in the same Availability Zone when appropriate
• Using VPC endpoints for AWS services
• Avoiding unnecessary cross-Region traffic
• Compressing responses
• Using CloudFront
• Designing data locality carefully

Do not move everything into one Availability Zone only to save money if availability is critical.

────────────────────

6.10 Optimize EBS

EBS cost depends on:

• Volume type
• Provisioned size
• Provisioned IOPS
• Throughput
• Snapshots

Review:

• Unused volumes
• Over-provisioned volumes
• Old snapshots
• Unused provisioned IOPS
• Volumes attached to stopped instances

Use the least expensive EBS type that meets latency and throughput requirements.

────────────────────

6.11 Use Savings Plans carefully with containers

For ECS or Kubernetes on EC2:

• Measure node-level baseline usage
• Use mixed On-Demand and Spot node groups
• Use Cluster Autoscaler or Karpenter
• Use Graviton node groups where compatible
• Avoid keeping large unused nodes
• Pack pods efficiently

The real waste often comes from poor scheduling rather than the EC2 price itself.

────────────────────

Part 2: Amazon S3

1. What is Amazon S3?

Amazon S3 is object storage.

An object consists of:

Object data + metadata + key

Objects are stored in buckets.

Example key:

company-data/2026/09/17/report.csv

S3 is not a traditional disk. It is designed for:

• Very high durability
• Large-scale storage
• Object-based access
• Independent scaling
• Data sharing through APIs

────────────────────

2. S3 cost components

S3 cost is not only “storage per GB.”

The main cost dimensions are:

1. Storage
2. Requests
3. Data retrieval
4. Data transfer
5. Replication
6. Lifecycle transitions
7. Data management features
8. Analytics and monitoring
9. Encryption-related service charges
10. Special storage systems such as S3 Express One Zone

A useful model is:

Total S3 cost =
Storage
+ request charges
+ retrieval charges
+ data transfer
+ management charges
+ replication charges
+ transition charges

────────────────────

3. S3 storage classes

3.1 S3 Standard

Intended for

• Frequently accessed data
• Websites
• Active application data
• Content distribution origin
• Real-time analytics
• User uploads

Characteristics

• High availability
• High durability
• No minimum storage duration
• No retrieval fee in the usual sense
• Higher storage price than archival classes

Use when

Objects are accessed regularly and predictably.

────────────────────

3.2 S3 Intelligent-Tiering

This class automatically moves objects between access tiers based on access patterns.

Useful for

• Unknown access patterns
• Changing access patterns
• Large data lakes
• Data that may become cold over time

Main tiers

• Frequent Access
• Infrequent Access
• Archive Instant Access
• Archive Access
• Deep Archive Access

Advantages

• Automatic cost optimization
• No manual access prediction
• Objects remain available through S3

Costs

There is a small monitoring and automation charge per object. This can be inefficient for:

• Very small objects
• Extremely short-lived objects
• Data where the monitoring cost is significant compared with storage

Good principle

Use Intelligent-Tiering when access behavior is uncertain, not automatically for every object.

────────────────────

3.3 S3 Standard-Infrequent Access

Usually called S3 Standard-IA.

Intended for

• Data accessed less frequently
• Backups
• Disaster-recovery copies
• Older but still immediately available files
• Long-lived user data

Characteristics

• Lower storage cost than Standard
• Retrieval charges apply
• Minimum billable storage duration
• Minimum billable object size
• Designed for data that is not accessed every day

Important warning

If data is accessed frequently, Standard-IA can cost more than Standard because of retrieval fees.

────────────────────

3.4 S3 One Zone-Infrequent Access

Usually called S3 One Zone-IA.

Intended for

• Infrequently accessed data
• Data that can be recreated
• Secondary backups
• Temporary data
• Data already replicated elsewhere

Characteristics

• Stored in one Availability Zone
• Lower storage cost
• Retrieval charge
• Less resilient to loss of that Availability Zone than multi-AZ classes

Do not use for

• The only copy of critical data
• Compliance data requiring multi-AZ resilience
• Irreplaceable records

────────────────────

3.5 S3 Glacier Instant Retrieval

Intended for

• Archive data requiring immediate access
• Medical images
• Financial records
• Media archives
• Historical data that is rarely accessed but must be retrieved quickly

Characteristics

• Lower storage cost
• Immediate retrieval
• Retrieval charges
• Minimum storage duration
• Minimum billable object size

The word “Glacier” does not necessarily mean slow retrieval. This class is designed for instant access.

────────────────────

3.6 S3 Glacier Flexible Retrieval

Previously known as S3 Glacier.

Intended for

• Archives accessed a few times per year
• Backup archives
• Long-term retention
• Data that can tolerate retrieval delay

Retrieval options

• Expedited
• Standard
• Bulk

Bulk retrieval is cheaper but slower.

Good for

• Compliance archives
• Old backups
• Historical datasets
• Legal records

────────────────────

3.7 S3 Glacier Deep Archive

Intended for

• Data accessed once a year or less
• Long-term preservation
• Regulatory archives
• Scientific records
• Digital preservation
• Government records

Characteristics

• Very low storage price
• Slow retrieval
• Retrieval charges
• Long minimum storage duration
• Usually requires planning for retrieval delay

This is usually the lowest-cost S3 storage class for long-term archival data.

────────────────────

3.8 S3 Express One Zone

S3 Express One Zone is designed for very high request performance and low latency.

Characteristics

• Single Availability Zone
• Very low latency
• High request rates
• Suitable for frequently accessed data requiring fast object operations
• Different pricing model from ordinary S3 classes

Good for

• High-performance analytics
• Machine learning feature data
• Interactive processing
• Latency-sensitive applications
• Temporary high-performance object storage

Cost caution

It may reduce request latency but is not automatically cheaper than Standard. Compare:

Storage cost + request cost + data transfer + operational benefit

────────────────────

3.9 S3 on Outposts

S3 on Outposts stores objects on AWS Outposts hardware in your own facility.

Good for

• Local data processing
• Data residency
• Low-latency on-premises workloads
• Disconnected or partially connected environments

It is not the same as ordinary regional S3 and has different operational requirements.

────────────────────

3.10 S3 Tables

S3 Tables are designed for tabular analytics data, particularly table formats such as Apache Iceberg.

Good for

• Data lakes
• Analytics tables
• Large-scale query workloads
• Table maintenance automation

This is a specialized data-lake capability rather than a simple replacement for every S3 bucket.

────────────────────

4. S3 storage-class comparison

┌─────────────────────┬───────────────────┬────────────────────────┬────────────────┬────────────────────────────┐
│ Storage class       │ Access pattern    │ Retrieval speed        │ Availability   │ Cost idea                  │
│                     │                   │                        │ design         │                            │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Standard            │ Frequent          │ Immediate              │ Multi-AZ       │ Higher storage, simple     │
│                     │                   │                        │                │ access                     │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Intelligent-Tiering │ Unknown or        │ Immediate or           │ Tier-dependent │ Autoc optimization     │
│                     │ changing          │ archive-dependent      │                │ plus monitoring            │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Standard-IA     │ Infrequent        │ Immediate              │ Multi-AZ       │ Lower storage, retrieval   │
│                     │                   │                        │                │ fees                       │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────â─────────────────┤
│ One Zone-IA         │ Infrequent and    │ Immediate              │ One AZ         │ Lower cost, lower          │
│                     │ recreatable       │                        │                │ resilience                 │
├─────────────────────┼───────────────────┼───────────────────────â─────────────┼────────────────────────────┤
│ Glacier Instant     │ Rare but          │ Immediate              │ Archive design │ Low storage, retrieval     │
│ Retrieval           │ immediate         │                        │                │ fees                       │
├─────────────────────┼──────────────────â─────────────────────┼────────────────┼────────────────────────────┤
│ Glacier Flexible    │ Rare              │ Minutes to hours       │ Archive design │ Very low storage           │
│ Retrieval           │                   │ depending option       │                │                            │
├───────────────â─┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Glacier Deep        │ Very rare         │ Longest retrieval      │ Archive design │ Lowest storage             │
│ Archive             │                   │                        │                │                          │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Express One Zone    │ Very high         │ Very low latency       │ One AZ         │ Performance-focused        │
│                 │ performance       │                        │                │ pricing                    │
├─────────────────────┼───────────────────┼────────────────────────┼────────────────┼────────────────────────────┤
│ Outposts            │ Local/on-premises │ Local              │ Customer       │ Specialized pricing        │
│                     │                   │                        │ facility       │                            │
└─────────────────────┴───────────────────┴────────────────────────┴────────────────┴───────────────────────────────────────

5. S3 lifecycle management

Lifecycle rules automatically transition or delete objects.

Example:

Day 0: S3 Standard
Day 30: Standard-IA
Day 90: Glacier Instant Retrieval
Day 180: Glacier Flexible Retrieval
Day 365: Glacier Deep Archive
Day 2555: Delete

A lifecycle design should be based on:

• Access frequency
• Data retention requirements
• Retrieval time requirement
• Minimum storage duration
• Object size
• Regulatory requirements

────────────────────

Important lifecycle trap: minimum storage duration

Some storage classes have minimum billable storage periods.

If an object is moved into an archival class and deleted too soon, AWS may still charge as though it remained for the minimum period.

Therefore, aggressive transitions can increase cost.

Example

If objects are moved to an archive class after 30 days but deleted after 35 days, the transition may be economically bad becauseect did not remain long enough.

────────────────────

6. S3 cost-saving methods

6.1 Select the correct storage class

Use access data, not assumptions.

A simple decision rule:

┌──────────────────────────────────────┬──────────────────────────────────────────┐
│ Question                             âecommended direction                    │
├──────────────────────────────────────┼──────────────────────────────────────────┤
│ Accessed frequently?                 │ Standard                                 │
├──────────────────────────────────────┼────────────────────────────────────────┤
│ Access pattern unknown?              │ Intelligent-Tiering                      │
├──────────────────────────────────────┼──────────────────────────────────────────┤
│ Accessed rarely but immediately?     │ Standard-IA or Glacier Instant Retrieval │
├──────────────────────────────────────┼──────────────────────────────────────────┤
│ Can tolerate one-AZ risk?            │ One Zone-IA                              │
├──────────────────────────────────────┼────â─────────────────────────────┤
│ Retrieval can take minutes or hours? │ Glacier Flexible Retrieval               │
├──────────────────────────────────────┼──────────────────────────────────────────┤
│ Retrieved once a year or less?       │ Glacier Deep Archive             ───────────────────────────────────┴──────────────────────────────────────────┘

────────────────────

6.2 Use lifecycle policies

Automate transitions and deletion.

Avoid manually moving millions of objects. Lifecycle rules provide:

• Consistency
• Lower operational work
• Reduced human error
• Automatic retention enforcement

────────────────────

6.3 Delete incomplete multipart uploads

Multipart uploads that are never completed can consume storage.

Create a lifecycle rule to abort incomplete multipart uploads after a reasonable number of days.

────────────────────

6.4 Delete expired object versions

Versioning protects against accidental deletion, but old versions remain billable.

Configure lifecycle rules for:

• ncurrent versions
• Delete markers
• Expired delete markers
• Incomplete multipart uploads

Do not delete versions before your recovery or compliance period ends.

────────────────────

6.5 Avoid unnecessary replication

Cross-Region Replication and Same-Region Replication improve resilience but increase:

• Storage cost
• Request cost
• Replication data transfer
• Operational complexity

Replicate only data that needs it.

──────────

6.6 Reduce object count when appropriate

S3 request charges are often affected by object operations.

Millions of very small objects can cause:

• High request overhead
• Higher metadata overhead
• Poor analytics performance
• Higher lifecycle-management overhead

For analytics, combine small files into appropriately sized objects, such as Parquet files. Do not combine objects blindly if individual access is required.

────────────────────

6.7 Use compression

Compression reduces:

• Storage
• Data transfer
• Query scanning
• Processing time

For analytics, columnar formats such as:

• Parquet
• ORC

often provide better compression and query efficiency than CSV or JSON.

────────────────────

6.8 Use S3 Select or query pushdown where appropriate

If an application needs only part of an object, avoid downloading the complete object.

For analytical workloads, use:

• Athena
• Redshift Spectrum
• EMR
âry pushdown
• Columnar formats

The goal is to reduce:

Bytes stored + bytes scanned + bytes transferred

────────────────────

6.9 Use CloudFront

For public or globally distributed content, CloudFront can:

• Cache objects near users
• Reduce repeated S3 requests
• Reduce origin data transfer
• Improve latency

S3 should usually be the origin, not the service directly serving every repeated download.

─────────────────â10 Use VPC endpoints

For private access from AWS resources, Gateway VPC Endpoints for S3 can avoid unnecessary NAT Gateway processing charges.

This is especially important when EC2 instances download large amounts of data from S3 through a NAT Gateway.

────────────────────

6.11 Analyze S3 Inventory and Storage Lens

Use:

• S3 Storage Lens
• S3 Inventory
• CloudWatch
• Cost Explorer
• AWS Budgets
• Cost and Usage Reports

Find:

• Old objects
• No versions
• Small objects
• Wrong storage classes
• Missing lifecycle policies
• High retrieval charges
• Replicated data
• Incomplete uploads

────────────────────

6.12 Use object tags and prefixes

Tags and prefixes allow cost allocation and policy control.

Example:

department=research
environment=production
data_class=regulated
project=genomics

This helps identify who is creating storage and which datasets should be archived.

───────â─────────

7. EC2 and S3 combined architecture

A common architecture is:

Users
  |
CloudFront
  |
S3 for static files
  |
EC2 or containers for application logic
  |
Database

Cost benefits

• S3 stores files cheaply and durably
• EC2 handles computation only
• CloudFront caches repeated downloads
• Auto Scaling adjusts compute capacity
• Lifecycle rules reduce long-term storage cost

Do not store large static files on an EC2 root volume unless there is a specific reason.

â────

8. Cost optimization formulas

EC2 effective hourly cost

Effective hourly cost =
Total commitment cost / committed hours

For a one-year commitment:

Effective hourly cost =
Upfront payment + hourly payments
--------------------------------
Number of hours in commitment

────────────────────

EC2 cost per transaction

Cost per transaction =
Compute cost + storage cost + network cost
------------------------------------------
Number of successful tranctions

This is more meaningful than comparing only hourly instance prices.

────────────────────

S3 monthly cost

S3 monthly cost =
Storage GB-month cost
+ request cost
+ retrieval cost
+ transfer cost
+ replication cost
+ management cost

────────────────────

S3 lifecycle decision

Move data to a cheaper class when:

Storage savings > transition cost + retrieval risk + management cost

A lower storage price is not always a lower total cost.

────────────────────

9. Practical decision examples

Example 1: Small company website

Requirements:

• Low and variable traffic
• Simple backend
• Static images
• Development environment

Recommended:

• T or small M instance
• Auto Scaling if traffic grows
• S3 Standard for active images
• CloudFront for delivery
• Stop development servers after working hours
• Consider Graviton

────────────────e 2: E-commerce platform

Requirements:

• Variable traffic
• High availability
• Database
• Product images
• Seasonal peaks

Recommended:

• M or C instances behind an Application Load Balancer
• Auto Scaling
• Savings Plan for baseline traffic
• Spot for background workers
• S3 Standard for active images
• CloudFront
• Lifecycle old product assets if appropriate
• Multi-AZ database design

────────────────────

Example 3: Machine-learning tining

Requirements:

• GPU training
• Jobs can restart
• Large datasets
• Results stored permanently

Recommended:

• P or Trn instances for training
• Spot where the framework supports interruption
• Checkpoints in S3
• S3 Standard or Intelligent-Tiering for active datasets
• Glacier for old model checkpoints
• Stop idle GPU instances immediately

────────────────────

Example 4: Compliance archive

Requirements:

• Data retained for seven yeareval
• Strong durability
• Retrieval can take hours

Recommended:

• S3 Glacier Deep Archive
• Lifecycle policy
• Versioning only when necessary
• Object Lock if regulatory immutability is required
• Separate retrieval budget
• Inventory and audit monitoring

────────────────────

10. Common mistakes

EC2 mistakes

1. Choosing instances based on size rather than measured utilization
2. Running development machines 24/7
3. Buying Reserved Instances before understanding workload stability
4. Using Spot for stateful workloads without checkpointing
5. Ignoring memory and monitoring only CPU
6. Paying for oversized EBS volumes
7. Ignoring data-transfer charges
8. Using expensive GPU instances with low GPU utilization
9. Selecting old instance generations without measuring total performance
10. Buying a commitment that is not fully utilized

────────────────────

S3 mistakes

1. Keeping every object in S3 Standarr
2. Sending frequently accessed objects to an archival class
3. Ignoring retrieval charges
4. Forgetting old object versions
5. Keeping incomplete multipart uploads
6. Using One Zone-IA for the only copy of critical data
7. Replicating all data across Regions without a business requirement
8. Storing millions of tiny files inefficiently
9. Downloading entire large objects when only a small part is needed
10. Creating lifecycle transitions that violate minimum storage durations

─────────â───────

11. A high-quality AWS cost-optimization process

Step 1: Measure

Collect:

• CPU
• Memory
• Network
• EBS I/O
• S3 access frequency
• Object age
• Retrieval volume
• Request volume
• Data-transfer volume

────────────────────

Step 2: Classify

Classify EC2 workloads as:

• Burstable
• CPU-bound
• Memory-bound
• Storage-bound
• GPU-bound
• Interruptible
• Stable baseline
• Highly variable

Classify S3 data quently accessed
• Infrequently accessed
• Archive
• Temporary
• Compliance-retained
• Recreatable
• Critical primary data

────────────────────

Step 3: Optimize architecture

Choose:

• Appropriate EC2 family
• Graviton where possible
• Auto Scaling
• Spot for fault-tolerant work
• S3 storage class
• Lifecycle rules
• CloudFront
• VPC endpoints
• Correct replication strategy

────────────────────

Step 4: Purchase commitments

Only after measuring stable usage, choose:

• Savings Plans
• Reserved Instances
• Capacity Reservations

Commit to the baseline, not the peak.

────────────────────

Step 5: Monitor continuously

Track:

• Cost per service
• Cost per application
• Cost per customer
• Cost per transaction
• Commitment utilization
• Idle resources
• S3 retrieval charges
• Budget alerts
• Unexpected data transfer

────â───

Final revision summary

EC2 families

┌────────┬───────────────────────────────┐
│ Family │ Main purpose                  │
├────────┼───────────────────────────────┤
│ T      │ Burstable workloads           │
├────────┼───────────────────────────┤
│ M      │ Balanced general purpose      │
├────────┼───────────────────────────────┤
│ A      │ AMD general purpose           │
├────────┼───────────────────────────────┤
│ C      │ CPU-intensive workloads       │
├────────┼────────────────────────â──┤
│ Hpc    │ High-performance computing    │
├────────┼───────────────────────────────┤
│ R      │ Memory-intensive workloads    │
├────────┼───────────────────────────────┤
│ X      │ Very large-memory workloads   │
├────────┼──────────────────────â────┤
│ U      │ Terabyte-scale memory         │
├────────┼───────────────────────────────┤
│ Z      │ High-frequency CPU and memory │
├────────┼───────────────────────────────┤
│ I      │ High-speed local NVMe storage │
├────────┼────────────────────â──────┤
│ D      │ Dense local storage           │
├────────┼───────────────────────────────┤
│ H      │ Large local HDD storage       │
├────────┼───────────────────────────────┤
│ P      │ GPU training and computation  │
├────────┼──────────────────â────────┤
│ G      │ Graphics and inference        │
├────────┼───────────────────────────────┤
│ Inf    │ ML inference                  │
├────────┼───────────────────────────────┤
│ Trn    │ ML training                   │
├────────┼────────────────â──────────┤
│ F      │ FPGA acceleration             │
├────────┼───────────────────────────────┤
│ DL     │ Deep learning acceleration    │
├────────┼───────────────────────────────┤
│ Metal  │ Direct physical server access │
└────────┴──────────────â────────────┘

Strongest EC2 savings techniques

1. Right-size instances
2. Stop idle non-production systems
3. Use Auto Scaling
4. Use Graviton where compatible
5. Use Spot for interruptible jobs
6. Use Savings Plans for stable baseline usage
7. Optimize EBS
8. Reduce data-transfer charges
9. Improve container packing
10. Measure cost per completed job or transaction

S3 storage classes

1. Standard
2. Intelligent-Tiering
3. Standard-IA
4. One Zone-IA
5. Glacier Instant Retrievr Flexible Retrieval
7. Glacier Deep Archive
8. Express One Zone
9. S3 on Outposts
10. S3 Tables for specialized tabular analytics

Strongest S3 savings techniques

1. Use the correct storage class
2. Apply lifecycle policies
3. Delete old versions
4. Abort incomplete multipart uploads
5. Compress and use Parquet or ORC
6. Reduce unnecessary replication
7. Use CloudFront for repeated downloads
8. Use VPC endpoints instead of NAT paths where appropriate
9. Control small-object growth
10. Monitor retrieval, request, transfer, and management costs

Core principle: Optimize for cost per useful result, not merely the lowest hourly price. A more expensive EC2 instance may be cheaper if it finishes the job faster, and a cheaper S3 storage class may become more expensive if retrieval charges are high.
