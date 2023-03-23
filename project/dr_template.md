# Infrastructure

## AWS Zones
Identify your zones here

- `us-east-2`
- `us-west-1`

## Servers and Clusters
- 3 EC2 Nodes for Website and API backend
- Kubernetes cluster with atleast 2 Nodes (as we dont have voting here we dont need 3) for monitoring stack
- 3 nodes for Database
- 3 nodes for redis

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Website & Backend API server | Hosts static website content and Backend API for the Ecommerce portal | t3.xlarge | 5 | currently we have it in just one region but we have them accross multiple availability Zones, we use a AMI for spinning up these nodes, There is no DR or deployments in other regions |
| Redis Cluster | Used for Caching frequently visited product pages and landing pages , this reduces load to db and increases UX as cache is kept in RAM and acheives low latency | t3.large | 3 | currently we have it in just one region but we have them accross multiple availability Zones, we use a AMI for spinning up these nodes and the redis cluster is run via docker, There is no DR or deployments in other regions |
| Database Cluster | Apllication database used to persist Product catalog , User catalog, Orders etc | db.t3.2xlarge | 3 | currently we have it in just one region but we have them accross multiple availability Zones, we have 1 master and 2 read replica, There is no DR or deployments in other regions |
| Monitoring Stack | EKS Comprises of Grafana and promethesus, used for collecting Applications HTTP metrics and alerting and synthetic Monitoring  | t3.large | 2 | currently we have it in just one region but we have them accross multiple availability Zones, we keep it in Kubernetes as its easy to manage, There is no DR or deployments in other regions |





### Descriptions
- Website & Backend API server  - Contains Static website built using react and Backend API server built with Flask, keeping in mind that we have high usage we are using 5 instances of t3.xlarge, The EC2 node have IP space in multiple availability zones and have redundancy
- Redis Custer: HA setup, Runs stable version of Redis 6.2.6, we need good RAM on these servers , we have 3 instances of t3.large each with 8 GB RAM 
- Database cluster:  Runs `Mysql 5.6` , Its a HA setup, supports failover we have a master where all write requests go and we have 2 read replica that load balances read queries
- Prometheus stack - we have installed prometheus-grafana on kubernetes with 2 worker nodes, we have used EKS so master Node is managed by AWS. we have relatively smaller nodes since these are on resource usage but we have redundacy 


## DR Plan
### Pre-Steps:
- We use same Terraform scripts for DR region, the tfstate bucket and region specific details vary , but in all other respect its identical to the TF scripts for Primary
- We run this script for DR region and test it to ensure that things will work when actual DR happens
- We backup DB to multiple buckets in separate region

## Steps:

- We have database replication setup to DR site , so incase of failover we wont suffer from data loss. We have a automatic Failover here using some custom script
- Our CNAME points to a Virtual IP, Incase of DR we just change Virtual IP to point to loadbalancer in DR site, We can have script that pings current primary to check if its up, incase it fails for max_fail threshold we trigger automatic Virtual IP update to DR loadbalancer