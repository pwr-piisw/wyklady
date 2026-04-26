# Cloud Computing Architecture

*Innovation is about value for business*

**Authors:** Marek Matczak, Maciej Małecki

---

## Cloud Computing

### Moore's Law: Is There a Limit?

Well, we cannot violate the laws of physics.

---

## Underlying Principles of Cloud Computing

### Parallel Computing

- Breaking a problem into *n* sub-problems each of which can be solved in parallel
- Solutions based on concurrent processing
- Multicore and multiprocessor systems

### Distributed Computing

- Distributed processing across multiple machines or computing nodes in a network
- Map-Reduce approach
- Horizontal scalability
- Use in both calculation and storage

---

## Evolution of Cloud Computing Models

A historical timeline showing how cloud computing models have evolved over time.

---

## SaaS / PaaS / IaaS

### Software as a Service (SaaS)

- Fully functional applications available via network
- Mostly with web or mobile user interface

### Platform as a Service (PaaS)

- Managed infrastructure
- Development and deployment tools provided

### Infrastructure as a Service (IaaS)

- Low-level resources available on demand
- Pay per use
- Servers, clusters, storage

---

## Map-Reduce

A programming model for processing and generating large data sets with a parallel, distributed algorithm.

---

## Characteristics of Cloud Solutions

### Scalability

Matching infrastructure size to demand — adequate for both very small and very large applications.

**Example:** A PoC or startup uses minimum infrastructure to keep costs low, while a production version supports mass usage.

### Elasticity

Allocation of resources can be changed dynamically in response to current demand, with reduction of infrastructure after periods of increased demand.

**Example:** Allocation of additional resources to a mobile operator around 12 o'clock on New Year's Eve.

### Resource Sharing

- Cost-effectiveness
- Multi-user infrastructure sharing

**Example:** Amazon Bedrock — shared GPU resources for underlying language models.

> The cloud can be a cost saver if managed well.

---

## Elasticity of the Cloud — E-Commerce Example

| Scenario | Action |
|----------|--------|
| Black Friday promo | **SCALE UP** |
| Normal work day | Steady state |
| Middle of the night | **SCALE DOWN** |

---

## On-Demand Provisioning

Resources are provisioned dynamically as needed, without manual intervention.

---

## Serverless Cloud Services

Examples of serverless services from AWS:

- **AWS Lambda** — serverless compute
- **Amazon Bedrock** — generative AI
- **Amazon Aurora** — relational database
- **Amazon DynamoDB** — NoSQL database
- **Amazon API Gateway** — API management
- **Amazon Simple Storage Service (Amazon S3)** — object storage

---

## Infrastructure as Code (IaC)

Automation and management of infrastructure through code instead of manual processes.

---

## Risks Related to the Cloud

Considerations around adopting cloud solutions, including security, vendor lock-in, compliance, and cost management.

---

## How Risks Can Be Mitigated

Strategies and best practices for addressing the identified cloud risks.

---

## Architecture of Cloud Applications

All cloud providers offer architecture frameworks for building cloud applications:

- **Azure Well-Architected Framework**
- **AWS Well-Architected Framework**
- **Google Cloud Architecture Framework**

All of these frameworks are built on the same pillars.

---

## Choosing Cloud Services

The choice of cloud services to use depends on the application type.

*Sources: Azure Documentation, AWS Documentation*
