# Keycloak and MySQL Deployment with Kubernetes

This project provides Kubernetes manifests to deploy a Keycloak server with a MySQL database as its backend. It includes configurations for deployment, secrets management, and service exposure.


## Table of Contents

1. Overview
2. Prerequisites
3. Manifests Included
4. Deployment Instructions
5. Environment Variables
6. Customization
7. Notes

## Overview

* Keycloak: An open-source Identity and Access Management (IAM) solution.
* MySQL: The database backend for Keycloak.
* The deployment includes readiness/liveness probes, resource management, and persistent storage.

## Prerequisites

1. Kubernetes cluster (Minikube, GKE, or similar).
2. kubectl command-line tool configured for your cluster.
3. Base64 encoding tool for managing secrets.


## Manifests Included

1. Keycloak Deployment

* Keycloak server deployed with environment variables for MySQL database connection.
* Custom configurations for probes, caching, and administrative settings.

2. MySQL Deployment

* MySQL database with pre-configured root and user credentials.
* Secrets for securely managing sensitive information.

3. Secrets

* Encrypted database credentials using Base64.

4. Services

* MySQL service exposed as a LoadBalancer for external access.

## Deployment Instructions

1. Deploy the MySQL Database and secrets:
```
kubectl apply -f mysql.yaml
```
3. Deploy Keycloak:
```
kubectl apply -f keycloak-<dev/prod>-deployment.yaml
```
5. Deploy svc:
```
kubectl apply -f keycloak-service.yaml
```
7. Verify the Deployments:

* Check pods:
```
      kubectl get pods
```
* Check services:
```
      kubectl get svc
```
## Environment Variables

Keycloak Deployment

Variable
Description
DB_HOST
Hostname of the MySQL database (default: mysql).
KC_DB_USERNAME
MySQL username for Keycloak.
KC_DB_PASSWORD
MySQL password for Keycloak.
KEYCLOAK_ADMIN
Admin username for Keycloak.
KEYCLOAK_ADMIN_PASSWORD
Admin password for Keycloak.

(db user-name and password Values are inject from secrets)
MySQL Deployment

Variable
Description
MYSQL_ROOT_PASSWORD
MySQL root password (from secret).
MYSQL_USER
MySQL username (from secret).
MYSQL_PASSWORD
MySQL user password (from secret).
MYSQL_DATABASE
Database name (default: keycloak_db).



## Customization

1. Persistent Volumes:

Replace emptyDir with PersistentVolumeClaim for production use.
Example:
```
volumes:
- name: mysql-persistent-storage
  persistentVolumeClaim:
    claimName: mysql-pvc
```
2. Resource Limits:

Adjust resource requests/limits in resources blocks as per the cluster capacity.

3. Admin URL:

Update the ```KC_HOSTNAME_ADMIN_URL``` and ```KC_HOSTNAME``` as per your external domain. If you use domain do need to create ssl for the domain.

4. Scaling:

Modify replicas in the deployments to scale the pods.

## Notes

Probes: The liveness and readiness probes ensure Keycloak is running and accessible.

Secrets Management: Secrets are Base64-encoded. Use the following commands to encode values:
```
echo -n "your-password" | base64
```

Testing:

Access Keycloak at ```http://localhost:8089/auth``` (default ```admin```: ```admin1```)
by running
```
kubectl port-forward svc/keycloak 8089:8089 
```
Production Recommendations:

* Replace emptyDir volumes with Persistent Volumes.
* Use Ingress to expose services securely with TLS.
