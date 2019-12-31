# Prometheus Operator 
 - The Prometheus Operator for Kubernetes provides easy monitoring definitions for Kubernetes services and deployment and management of Prometheus instances.
 - We are going to use the Prometheus Operator to:
     - Perform the initial installation and configuration of the full Kubernetes-Prometheus stack  
         - Prometheus servers
         - Alertmanager
         - node-exporter
         - Servicemonitor
         - kube-state-metrics
     - Define metric endpoint autoconfiguration using the ServiceMonitor entities
     - Customize and scale the services using the Operator CRDs and ConfigMaps, making our configuration fully portable and declarative     

 

# DevOps Platform Health Monitoring

This repository contains the predefined dashboards and configuration files to do service discovery of the devops tools and scrape the metrics from the DevOps tools along using Prometheus Operator

## A. Install Prometheus Operator 
1. helm install stable/prometheus-operator. You can customize the values.yaml according to your environment with specific components installed and with RBAC enabled / disabled. 

        
