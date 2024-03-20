<!-- ABOUT THE PROJECT -->
## About The Project

In this project, [Simple Java App](https://github.com/benc-uk/java-demoapp/) was monitored end-to-end from the application development phase to the event-based deployment strategy of the application. Almost all component of this studies are As a Code. 

The Monitoring of simple Java App are 2 different way. First one is editing source code. The second is monitoring via using Sidecar Container.

<!-- GETTING STARTED -->

### Prerequisites

The services in this project have been deployed to a Kubernetes environment. You can use Public Cloud, or local development environments. It doesn't matter.
Also, the installation of brew, kubectl, istioctl, sloth tools  should be on your computer.


### Installation


1. Install or prepare orchestiration environment
2. Clone Repostory
   ```sh
   git clone https://github.com/yunusgun/observability.git
   ```
3. Install Helm Repostory
   ```sh
   helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
   helm repo add sloth https://slok.github.io/sloth
   helm repo add istio https://istio-release.storage.googleapis.com/charts
   helm repo add argo https://argoproj.github.io/argo-helm
   helm repo add grafana https://grafana.github.io/helm-charts
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   ```
4. Prepering Kubernetes Namespace
   ```sh
   kubectl create ns monitoring
   kubectl create ns cicd
   kubectl create ns java-app
   kubectl create ns java-app-auto-instrumentation
   kubectl create namespace istio-system
   ```
5. Files on this project
   
   ```sh
   tree observability
   ```
   ```
    observability/
    ├── CI-CD
    │ ├── ArgoCd-Rollout
    │ │ ├── HorizontialPodScale
    │ │ │ └── java-app-hpa-rollout.yaml
    │ │ ├── JavaDemoApp
    │ │ │ └── javademoappmodified.yaml
    │ │ └── JavaDemoAppWithAutoInstrumentation
    │ │ ├── README.md
    │ │ └── java-demo-app-auto-instrumentation.yaml
    │ └── Jenkins
    │ ├── Java-Demo-App
    │ │ └── Jenkinsfile
    │ └── Java-Demo-App-With-Auto-Instrumentation
    │ ├── Jenkinsfile
    │ └── README.md
    ├── Monitoring
    │ ├── Alerts
    │ │ ├── argocd-alerts.yaml
    │ │ ├── jenkins-alerts.yaml
    │ │ ├── jvm-alerts.yaml
    │ │ ├── kubernetes-alerts.yaml
    │ │ └── latency-record-rule.yaml
    │ ├── Dashboards
    │ │ ├── argocd.yaml
    │ │ ├── jenkins.yaml
    │ │ ├── jvm-metrics.yaml
    │ │ ├── kubernetes-deployment.yaml
    │ │ ├── sloth.yaml
    │ │ └── springbootapm.yaml
    │ ├── Log
    │ │ └── Helm-values
    │ │ ├── fluentbit-values.yaml
    │ │ └── loki-distributed-values.yaml
    │ ├── Metric
    │ │ ├── Helm-values
    │ │ │ ├── p8s-stack-values.yaml
    │ │ │ └── sloth-values.yaml
    │ │ └── SLO
    │ │ ├── Generated
    │ │ │ ├── java-app-service-level-availability.yaml
    │ │ │ └── java-app-service-level-latency.yaml
    │ │ └── Templates
    │ │ ├── sloth-k8s-tamplate-availability.yaml
    │ │ └── sloth-k8s-tamplate-latency.yaml
    │ ├── Profiling
    │ │ └── Helm-values
    │ │ └── grafana-pyroscoe-values.yaml
    │ └── Trace
    │ └── Helm-values
    │ ├── otel-collector-values.yaml
    │ └── tempo-values.yaml
    └── README.md

    23 directories, 30 files
    ```
6. Installation
   - Prometheus Stack Installation 
   ```sh
   helm upgrade --install prometheus prometheus-community/kube-prometheus-stack -f ./observability/Monitoring/Metric/Helm-values/p8s-stack-values.yaml -n monitoring
   ```
   - Loki Distributed Installation 
   ```sh
    helm upgrade --install loki grafana/loki-distributed -f ./observability/Monitoring/Log/Helm-values/loki-distributed-values.yaml -n monitoring
   ```
   - Tempo  Installation 
   ```sh
    helm upgrade --install tempo grafana/tempo -f ./observability/Monitoring/Trace/Helm-values/tempo-values.yaml -n monitoring
   ```
   - Pyroscope  Installation 
   ```sh
    helm upgrade --install pyroscope grafana/pyroscope -n monitoring -f Monitoring/Profiling/Helm-values/grafana-pyroscoe-values.yaml
   ```
   - ArgoCD  Installation 
   ```sh
    helm upgrade --install argo argo/argo-cd -f CI-CD/ArgoCd-Rollout/argocd-values.yaml -n cicd
   ```
   - Argo Rollout  Installation 
   ```sh
    helm upgrade --install argo-rollout  argo/argo-rollouts  -f CI-CD/ArgoCd-Rollout/argo-rollout-values.yaml -n cicd
   ```
   - Istio  Installation 
   ```sh
    helm upgrade --install istio-base istio/base -n istio-system --set defaultRevision=demo
    helm upgrade --install istiod istio/istiod -n istio-system --wait
   ```

   
