---
layout: post
title:  "OpenTelemetry with OpenSearch: A Complete Guide to Modern Observability"
authors:
 - synhershko
date: 2025-09-29
categories: open-telemetry,observability
meta_keywords:
meta_description:
excerpt: OpenSearch Benchmark 2.0 redefines the benchmarking experience and introduces long-awaited features that offer users the essential tools needed to measure, track, and optimize OpenSearch performance.
has_math: False
has_science_table: False
---

Modern distributed systems require comprehensive observability to understand performance bottlenecks, track errors, and maintain optimal user experiences. OpenTelemetry has emerged as the industry standard for observability, providing a vendor-neutral framework for collecting logs, metrics, and traces from applications. When combined with OpenSearch's powerful search and analytics capabilities, organizations can achieve end-to-end visibility across their entire technology stack.

OpenSearch offers a compelling platform for OpenTelemetry data due to its proven scalability, rich querying capabilities, and comprehensive dashboard features. With 15+ years of battle-tested technology behind it, OpenSearch can handle petabyte-scale observability data while providing the flexibility to perform complex aggregations and text searches across all three pillars of observability.

## Understanding the Three Pillars of Observability

### Logs, Metrics, and Traces Explained

OpenTelemetry standardizes three fundamental types of observability data:

**Logs** represent timestamped lines of text that capture discrete events within your application. They provide contextual information about what happened at specific moments in time.

**Metrics** consist of timestamped numerical measurements that track quantitative data over time, such as response times, error rates, or resource utilization.

**Traces** offer the most sophisticated observability capability by tracking requests as they flow through distributed systems. A trace consists of multiple spans, where each span represents a unit of work like a function call, database query, or API request.

<img width="1201" height="654" alt="The 3 Pillars of Observability" src="https://github.com/user-attachments/assets/d2280352-2d72-4e46-9b97-07dbd666a51f" />

### Distributed Tracing in Action

Consider a typical e-commerce application where a user places an order. With distributed tracing, you can visualize the complete request journey:

1. Initial API call to the order service
2. User authentication check
3. Inventory verification
4. Payment processing
5. Database updates
6. Email notification triggers

Each of these steps becomes a span within a single trace, connected by a unique trace ID that flows through your entire system. This enables you to pinpoint exactly where bottlenecks occur and understand dependencies between services.

Visualizing traces in OpenSearch's Trace Analytics would then look like this:

<img width="1569" height="717" alt="image" src="https://github.com/user-attachments/assets/977d50ae-1e8c-4aa2-a63f-7d9ec0cb567d" />

## Implementing OpenTelemetry with OpenSearch

### Architecture Overview

A complete OpenTelemetry implementation with OpenSearch involves several key components:

- **Instrumentation** layer that captures observability data from your applications
- **OpenTelemetry Collector** that receives, processes, and forwards telemetry data
- **OpenSearch cluster** for storing and indexing the observability data
- **OpenSearch Dashboards** for visualization and analysis
- (optional) **Data Prepper** for creating service maps and some additional ingestion support.


<img width="518" height="655" alt="image" src="https://github.com/user-attachments/assets/0d5c3a63-ebbe-4d70-b2a9-80af48becf94" />


### Auto-Instrumentation Setup

The easiest way to get started is through auto-instrumentation, which requires no code changes to your existing applications. Here's an example for a Python application:

```bash
# Install OpenTelemetry packages
pip install opentelemetry-api
pip install opentelemetry-sdk
pip install opentelemetry-auto-instrumentation

# Run your application with instrumentation
opentelemetry-instrument python your_application.py
```

This automatically captures common operations like HTTP requests, database calls, and function executions without requiring manual code modifications.

### OpenTelemetry Collector Configuration

The OpenTelemetry Collector acts as a crucial intermediary that receives, processes, and exports telemetry data. Here's a basic configuration example:

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318

processors:
  batch:
  memory_limiter:
    limit_mib: 512
  probabilistic_sampler:
    sampling_percentage: 10

exporters:
  opensearch:
    endpoint: https://your-opensearch-cluster:9200
    index_prefix: "otel"
    auth:
      username: "admin"
      password: "admin"

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [memory_limiter, probabilistic_sampler, batch]
      exporters: [opensearch]
    metrics:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [opensearch]
    logs:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [opensearch]
```

This configuration enables the collector to receive OpenTelemetry data via OTLP protocol, apply sampling and batching for efficiency, and export everything to your OpenSearch cluster.

## Analyzing Observability Data in OpenSearch

### Trace Analytics Dashboard

OpenSearch Dashboards provides built-in trace analytics capabilities that transform raw telemetry data into actionable insights. The trace analytics interface offers several key features:

**Service Map Visualization**: A graphical representation showing how services interact with each other, including latency, error rates, and throughput metrics between service connections.

**Service Performance Overview**: Detailed performance metrics for each service, including average response times, error percentages, and request volumes over time.

**Error Analysis**: Comprehensive error tracking that groups similar errors together and shows their frequency and impact across your system.

### Query and Investigation Workflows

Different investigation scenarios require different entry points into your observability data:

**User-Specific Issues**: When a user reports a problem, they can provide a trace ID that allows support teams to examine the complete request flow and identify the exact failure point.

**Service Health Monitoring**: Regular monitoring of service performance metrics helps identify degrading services before they impact users significantly.

**Error Investigation**: Systematic analysis of error patterns helps identify recurring issues and their root causes across your distributed system.

**Dependency Analysis**: Understanding service relationships and communication patterns helps with capacity planning and architectural decisions.

The Pipeline Processing Language (PPL) in OpenSearch enables sophisticated querying across all observability data types, allowing for complex correlations between logs, metrics, and traces within a single query interface.

<img width="1681" height="1022" alt="image" src="https://github.com/user-attachments/assets/72890759-2e81-4439-9103-3d1cf1d4457d" />

### Advanced Use Cases

OpenTelemetry with OpenSearch proves particularly valuable in serverless environments where traditional monitoring approaches fall short. Serverless functions often have complex interaction patterns and short lifecycles that make debugging challenging without comprehensive distributed tracing.

Application Performance Monitoring (APM) represents another critical use case, especially for organizations running microservices architectures where request paths span multiple services, databases, and external APIs. The ability to trace requests end-to-end while correlating with relevant logs and metrics provides unprecedented visibility into application behavior.
