# 🏦 Enterprise Decoupled Financial Transaction Engine

[![AWS](https://img.shields.io/badge/AWS-SQS%20%7C%20SNS%20%7C%20DynamoDB%20%7C%20KMS-orange?logo=amazon-aws)](https://aws.amazon.com/)
[![LocalStack](https://img.shields.io/badge/Emulation-LocalStack%203.8-blue?logo=docker)](https://localstack.cloud/)
[![Security](https://img.shields.io/badge/Compliance-PCI--DSS%20Ready-green)](https://aws.amazon.com/compliance/pci-dss-level-1-faqs/)
[![Docker](https://img.shields.io/badge/Container-Docker%20Desktop-2496ED?logo=docker)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An asynchronous, resilient payment settlement and queue buffer architecture engineered to handle flash-sale traffic spikes. Built using **Amazon SQS** with dead-letter queue circuit breakers, **Amazon SNS** multi-channel broadcast fan-out, **Amazon DynamoDB** immutable transaction ledgers, and **AWS KMS** cryptographic encryption at rest.

---

## 🏛️ Architecture Topology

```mermaid
flowchart LR
    Checkout["🛒 High-Traffic Checkout"] --> SQS["📬 SQS: Transaction Queue<br><i>financial-transactions-queue</i>"]
    SQS -->|Poison Message: 3x Retries Failed| DLQ["⚠️ SQS: Dead Letter Queue (DLQ)<br><i>transactions-dead-letter-queue</i>"]
    SQS --> Worker["⚙️ Payment Settlement Worker"]
    Worker --> DynamoDB[("📊 DynamoDB: TransactionLedger<br><i>Encrypted Financial Records</i>")]
    Worker --> SNS["📢 SNS: Settlement Alerts<br><i>financial-alerts-topic</i>"]
    SNS --> Customer["📧 Customer Payment Receipt"]
    SNS --> Warehouse["📦 Warehouse Order Dispatch"]
    KMS["🔐 AWS KMS Key<br><i>Customer Managed Key</i>"] -.->|Envelope Encryption| SQS
    KMS -.->|Storage Encryption| DynamoDB
