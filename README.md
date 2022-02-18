# Kubernetes Cluster & Prometheus

This repository contains ansible scripts to deploya KOPS cluster, nginx ingress controller and it deploys a sample application. 

The ansible script will benchmark the cluster and the application against (Request Per Second) RPS & Throughput, with the help of prometheus we will have detailed insights into cluster resources. 


## Prerequisites

* python 3.6.X
* ansible 2.11.X
* Linux(RHEL & Ubuntu) or Mac OS
* pip3
* AWS Account Credentials
* Access to root user on the system. 

# Namespace Architecture

![namespace pod](/image/kops-ingress.png?raw=true "Title")

## Components Being Used

- Ansible
- AWS
- Helm
- kubectl
- [KOPS](https://kops.sigs.k8s.io/)
- Nginx Ingress
- Prometheus
- Python
- Taskset
- [wrk](https://github.com/wg/wrk)

## Ansible Roles Overview

Ansible roles are being used to ensure reusability, summary of each role as below.

|Ansible Role| Description|
|------------|------------|
|Benchmark| Benchmark the cluster using  tools like taskset & wrk|
|Create Cluster| Create cluster using KOPS|
|Deployment| Deploying simple web application|
|Generate Report| Extract reports mentioned above from prometheus and convert it into CSV|
|Helm| Install helm on the executing machine|
|Ingress & Prometheus |Install Nginx ingress controller & pronetheus |
|Kops & Kubectl|Install kops & kubectl |
|Networking| Create AWS VPC & route54 internal hosted zone|
|Stat Store| S3 bucket for KOPS to store its state file|



## How To Run

Once above mentioned prerequisites are met, cluster can be deployed following below steps.

Step 1: Install Dependency like helm, kops etc
```
# ansible-playbook dependency.ayml
```

Step 2: Deploy cluster & application
```
# ansible-playbook main.yaml
```

## How To Access CSV File

Last role to be executed is **generate_report**. CSV file will be created in the same folder where ansible is being triggered.

### Prometheus Query being used

|Query|Purpose|File Name|
|-----|-------|---------|
|*nginx_ingress_controller_nginx_process_requests_total*| Extract requests which is passing through ingress controller|**request.csv**|
|*nginx_ingress_controller_nginx_process_resident_memory_bytes*|Extract ingres controller memory usage|**memory.csv**|
|*nginx_ingress_controller_nginx_process_cpu_seconds_total*|Extract ingres controller CPU usage|**cpu.csv**|
  

## How To Cleanup

To save cost resources can be deleted using cleanup role. AWS VPC configuration will not be delete for future use.

```
# ansible-playbook cleanup.yaml 
```

## Reference & Credit

To implement this task I have used many public resources, you can refer to the below links for more details.


* [prometheus queryig](https://prometheus.io/docs/prometheus/latest/querying/examples)

* [Nginx Monitorig](https://kubernetes.github.io/ingress-nginx/user-guide/monitoring/)

* [Prometheus Installation](prometheus-and-grafana-installation-using-service-monitors)

* [Nginx Performance](https://www.nginx.com/blog/testing-the-performance-of-nginx-and-nginx-plus-web-servers/)


# prometheus graphs

Graphs after running the benchmark

![Ingress Requests](/image/ingress_requests.png?raw=true "Ingress Requests") 

![Ingress Memeory](/image/Ingress_Memory.png?raw=true "Ingress Memeory") 

![Ingress CPU](/image/Ingress_CPU.png?raw=true "Ingress CPU") 