# DevSecOps Challenge

## Monitoring strategy of a new production application

To ensure proper monitoring of the application and its infrastructure,
it's important to understand the requirements of the application.
Therefore, the following questions should be answered:

* What kind of application is it?
* What is the application's purpose?
* Who are the users of the application?
* Which components contain the application?
* Where is the application deployed?
* How critical is the application?
* What are the alerting thresholds for the application and its infrastructure?
* What are the security requirements for the application?
* What are the backup and disaster recovery requirements for the application?

### What to monitor?

To ensure holistic monitoring of the application, four key areas should be monitored:

#### Application Performance Monitoring (APM)

* Server health (CPU, memory and disk)
* Error rates (exceptions, errors and warnings)
* Response time (request latency)
* Request rates (number of requests per second)
* Instances (number of running application instances)
* Availability (uptime)

#### Infrastructure Monitoring

* Cluster health (Node and pod health, control plane health, etc.)
* Resource usage (CPU, memory, disk and network)
* Status of other components (database, cache, queue, etc.)

#### Application Security Monitoring

* Intrusion detection (e.g., SQL injection)
* Vulnerabilities (e.g., CVEs)
* Configuration (e.g., misconfigurations)

#### Application Logging

* Centralized logging (one platform for all logs)
* Structured logging (e.g., JSON)
* Alerting (on critical events e.g., multiple permission denied errors)
* Traces (for distributed tracing)

### Tools to use

There are multiple tools that can be used to monitor the application and infrastructure.
Since Prometheus and Grafana are the most popular tools, are well integrated with Kubernetes,
are open source and are extensible, I recommend using them.

#### Application Performance Monitoring (APM)

| Metric/Concern | Tool(s)                                                                                                                 |
|----------------|-------------------------------------------------------------------------------------------------------------------------|
| Server health  | [node-exporter](https://github.com/prometheus/node_exporter)                                                            |
| Error Rates    | [Prometheus client library](https://prometheus.io/docs/instrumenting/clientlibs/) (e.g., prom-client, micrometer, etc.) |
| Response Time  | [Istio](https://istio.io/latest/docs/concepts/observability/#metrics)                                                   |
| Request Rates  | [Istio](https://istio.io/latest/docs/concepts/observability/#metrics)                                                   |
| Instances      | [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)                                                  |
| Availability   | [Prometheus `up`-Metric](https://prometheus.io/docs/concepts/jobs_instances/)                                           |

#### Infrastructure Monitoring

| Metric/Concern             | Tool(s)                                                                                                                                             |
|----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Cluster health             | [node-exporter](https://github.com/prometheus/node_exporter) for nodes, [cAdvisor](https://prometheus.io/docs/guides/cadvisor/) for pods/containers |
| Resource usage             | [node-exporter](https://github.com/prometheus/node_exporter) for nodes, [cAdvisor](https://prometheus.io/docs/guides/cadvisor/) for pods/containers |
| Status of other components | Specific exporters (e.g., postgres-exporter, etc.)                                                                                                  |

#### Application Security Monitoring

| Metric/Concern  | Tool(s)                                                        |
|-----------------|----------------------------------------------------------------|
| IDS             | [falco](https://falco.org/)                                    |
| Vulnerabilities | Shift left with [Trivy](https://github.com/aquasecurity/trivy) |
| Configuration   | Shift left with Trivy or [Checkov](https://www.checkov.io/)    |

#### Application Logging

| Metric/Concern      | Tool(s)                                                                                                                                           |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralized logging | [Grafana Loki](https://grafana.com/docs/loki/latest/)                                                                                             |
| Structured logging  | JSON (with e.g., [winston](https://github.com/winstonjs/winston))                                                                                 |
| Alerting            | [Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/) or [Grafana alerting](https://grafana.com/docs/grafana/latest/alerting/) |
| Traces              | [Grafana Tempo](https://grafana.com/docs/tempo/latest/)                                                                                           |

## Migration of a monolithic application to kubernetes

Since there are no goals or KPIs defined for the migration, I would use a lift and shift approach.
I would also advise first using a development environment to test the migration before deploying it to production.
To ensure a smooth migration if possible, I would use a canary release strategy. If this is not possible, I would
recommend using a blue/green deployment strategy.

For the migration of a monolithic application to kubernetes, the following phases should be considered:

### 1. Analysis

Initially, the application should be analyzed to answer the following questions:

* What kind of application is it?
* What is the application's purpose?
* Who are the users of the application?
* What additional dependencies does the application have?
* Does the application use a session logic?
* On which network and infrastructure is the application deployed?
* How critical is the application?
* How is the application configured?
* What is currently monitored?
* Which system components are necessary for the application?

### 2. Containerization

Now the application should be containerized and migrated to kubernetes. Therefore, the following steps should be
considered:

* Create a docker image for the application
* Create a kubernetes deployment for the application
    * Create kubernetes manifests for the application (e.g., deployment, service, ConfigMaps, Secrets, ServiceAccounts,
      etc.)
    * Use labels and annotations to organize and manage resources
    * Implement health checks for the
      application ([liveness, readiness and startup probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/))
    * Define network policies to restrict traffic between pods
    * Assign a ServiceAccount to the application for access control
* Set up monitoring and logging for the application

### 3. Deployment

Before the application can be tested, it must be deployed to a development environment.
Therefore, the following steps should be considered:

* Set up a CI/CD Pipeline for the application (e.g., GitHub Actions, Jenkins, Argo, etc.)
* Integrate a container registry (e.g., Docker Hub, GitHub Container Registry, Harbor, etc.)
* Automate the deployment (e.g., FluxCD or ArgoCD)

### 4. Decomposition

Now the application should be decomposed into microservices. Therefore, the following steps should be considered:

* Identify the application's components
* Create a service for each component
* Use an API gateway to expose the application's components to the outside world and route traffic to the new components

### Potential pitfalls

* The application must be adapted before the migration
* Sidecar containers are required for any dependencies
* External dependencies must remain accessible (e.g., database in a different domain)
* The application must be tested before the migration
* A backup strategy must be in place in case the migration fails
* Latency and/or performance issues may occur


## The serverless approach

"Serverless" is a term used to describe applications that are not managed by a server.
The serverless approach is a new approach to application development that is based on the idea of
"serverless computing". 
It's a method of providing computing resources on demand, without the hassle of worrying about the infrastructure.

Examples of serverless services are:
* [AWS Lambda](https://aws.amazon.com/lambda/)
* [Google Cloud Run-Functions](https://cloud.google.com/functions)
* [Azure Functions](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview)
* [Knative](https://knative.dev/docs/)
