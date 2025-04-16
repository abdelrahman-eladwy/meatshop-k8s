# ML7MA meatshop Application

This project demonstrates a complete Kubernetes deployment for a meatshop application called ML7MA, featuring both frontend and backend components with a MySQL database. The project showcases various Kubernetes concepts and deployment strategies, from basic manifests to a full Helm chart implementation.

## Project Overview

ML7MA is a meatshop platform with:
- React frontend (NGINX-served)
- Django backend API
- MySQL database for persistent storage

The application is deployed using Kubernetes, with configurations for local development using Kind and production-ready configurations via Helm charts.

## Architecture

       Frontend (NGINX) <---> Backend (Django) <---> Database (MySQL)
              |                    |
              |                    |
              v                    v
       www.ml7ma.com        www.ml7maapi.com


## Components

### Docker Images
- Frontend: `borhom11/frontend:1.0.5` - NGINX serving React application
- Backend: `borhom11/backend:1.0.1` - Django REST API
- Database: `mysql:8.0.11` - MySQL database server

### Kubernetes Resources

#### Core Infrastructure
- **StatefulSet** for MySQL to ensure stable network identities and persistent storage
- **Deployments** for both frontend and backend services
- **Services** to provide network access to deployments
- **ConfigMaps** for frontend configuration
- **Secrets** for sensitive data (database credentials, Django admin credentials)
- **Ingress** to route external traffic to the application

#### Jobs
- Database initialization job
- Django superuser creation job

#### Storage
- Persistent volume claims for MySQL data

## Project Structure

```
├── backend/                 # Kubernetes manifests for backend service
├── frontend/                # Kubernetes manifests for frontend service
├── mysql/                   # Kubernetes manifests for MySQL database
├── ml7ma-helm/              # Helm chart for the entire application
│   └── ml7ma/
│       ├── templates/       # Templated Kubernetes manifests
│       ├── values.yaml      # Default configuration values
│       └── Chart.yaml       # Chart metadata
├── ingress.yml              # Ingress configuration
└── kind-cluster.yml         # Kind cluster configuration for local development
```
![Alt text](Untitled.png)
## Key Features

### Infrastructure as Code
- All infrastructure defined as Kubernetes YAML manifests
- Parameterized templates via Helm chart for easy deployment across environments

### Database Management
- StatefulSet for stable database deployment
- Automatic database initialization
- Persistent storage for data durability

### Security
- Secrets management for sensitive credentials
- Environment variables for configuration

### Configuration Management
- ConfigMaps for non-sensitive configuration data
- Frontend configuration injection

### CI/CD Ready
- Ready for integration with CI/CD pipelines
- Versioned Docker images

### Networking
- Service discovery between components
- Ingress for external access with hostname-based routing

### Local Development
- Kind cluster configuration for local development and testing

## Deployment Instructions

### Local Development with Kind

1. Create a local Kind cluster:
   ```bash
   kind create cluster --config kind-cluster.yml
   ```

2. Deploy NGINX Ingress Controller:
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
   ```

3. Deploy MySQL:
   ```bash
   kubectl apply -f mysql/
   ```

4. Deploy backend and frontend:
   ```bash
   kubectl apply -f backend/
   kubectl apply -f frontend/
   ```

5. Deploy ingress:
   ```bash
   kubectl apply -f ingress.yml
   ```

6. Update your hosts file to point to localhost:
   ```
   127.0.0.1 www.ml7ma.com www.ml7maapi.com
   ```

### Production Deployment using Helm

1. Install using Helm:
   ```bash
   helm install ml7ma ./ml7ma-helm/ml7ma
   ```

2. Upgrade an existing deployment:
   ```bash
   helm upgrade ml7ma ./ml7ma-helm/ml7ma
   ```

3. Customize deployment:
   ```bash
   helm install ml7ma ./ml7ma-helm/ml7ma --set mysql.rootPassword=newPassword --set frontend.replicaCount=3
   ```

## Advanced Features

### Database Migrations and Initialization
The deployment includes an init container that runs Django migrations to set up the database schema automatically. After migrations, a job creates the Django superuser using credentials from Kubernetes secrets.

### High Availability
The frontend deployment is configured with multiple replicas for high availability. The backend and database can be scaled as needed.

### Resource Management
Resource requests and limits are configured for the frontend to ensure appropriate resource allocation.

## Technologies Used

- **Kubernetes**: Container orchestration
- **Helm**: Package management for Kubernetes
- **Docker**: Containerization
- **Kind**: Local Kubernetes development
- **NGINX**: Web server and reverse proxy
- **Django**: Python web framework
- **MySQL**: Relational database
- **React**: Frontend JavaScript framework

## Best Practices Implemented

- Separation of concerns between application components
- Parameterized templates for flexible deployment
- Proper resource allocation
- Secrets management for sensitive data
- Persistent storage for stateful components
- Init containers for proper application initialization
- Clean separation of environment-specific configuration

This project demonstrates a complete end-to-end Kubernetes deployment strategy for a modern web application, incorporating DevOps best practices for containerization, orchestration, and configuration management.
