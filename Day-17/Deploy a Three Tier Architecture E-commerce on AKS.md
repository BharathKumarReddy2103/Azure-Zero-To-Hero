**Deploying a Multi-Service E-Commerce Application on AKS using Kubernetes and Helm**

**Introduction**

In this guide, we will deploy a **multi-service e-commerce application** onto an **Azure Kubernetes Service (AKS) cluster**. This application follows a **three-tier architecture** and consists of **eight microservices**, utilizing databases like **MySQL** and **MongoDB**, along with **Redis** as an in-memory datastore.

We will cover the **architecture, containerization, Kubernetes manifests, Helm charts, Ingress configuration**, and **best practices for deployment on AKS.**

**Application Architecture**

The e-commerce application used in this guide is based on **IBM's Instana Robot Shop**, a proof-of-concept microservices application designed for learning and demonstration purposes. The application is structured as follows:

**Key Components**

**•	Microservices:** 8 independent services written in different languages.

**•	Databases:** MySQL for structured data, MongoDB for user data, and Redis for caching.

**•	Kubernetes Objects:** Deployments, StatefulSets, Persistent Volumes, Storage Classes, and Ingress controllers.

**Microservices Overview**

| **Microservice**     | **Function**                                              | **Technology Stack** |
|----------------------|----------------------------------------------------------|----------------------|
| **User Service**     | Handles user registration, authentication, and profile management. | Python |
| **Catalog Service**  | Manages product listings, descriptions, and categories.  | Node.js |
| **Cart Service**     | Manages the shopping cart for logged-in users.           | Node.js |
| **Payment Service**  | Processes payments and integrates with external payment gateways. | Python |
| **Shipping Service** | Calculates shipping costs based on location and availability. | Java |
| **Dispatch Service** | Manages order fulfillment and delivery tracking.         | Go |
| **Ratings Service**  | Stores and retrieves product ratings dynamically.        | PHP + Redis |
| **Web Service**      | Frontend user interface, served via Nginx.               | HTML + Angular |

**Database Architecture**

**•	MySQL:** Stores structured data, such as product information.

**•	MongoDB:** Stores user account details and authentication data.

**•	Redis:** Used for fast caching of product ratings.

**Step 1: Setting Up the AKS Cluster**

Before deploying the application, we need to set up an **Azure Kubernetes Service (AKS)** cluster.

**Create an AKS Cluster**

```sh
az aks create --resource-group ecommerce-demo \
              --name three-tier \
              --node-count 2 \
              --enable-managed-identity \
              --generate-ssh-keys
```

After creation, verify the cluster is running:

```sh
az aks get-credentials --resource-group ecommerce-demo --name three-tier
kubectl get nodes
```

**Step 2: Containerizing the Microservices**

Each microservice is containerized using **Docker**. Below is an example **Dockerfile** for the **Cart Service** (Node.js-based):

```sh
FROM node:16-alpine
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD ["node", "server.js"]
```

Similarly, a **Python-based microservice (Payment Service)** uses:

```sh
FROM python:3.9
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

All microservices are containerized in this manner.

**Step 3: Deploying with Kubernetes and Helm**

Instead of managing multiple **Kubernetes manifests**, we use **Helm** for deployment.

**Helm Chart Structure**

```sh
helm/
 ├── templates/
 │   ├── deployment.yaml
 │   ├── service.yaml
 │   ├── ingress.yaml
 │   ├── statefulset.yaml
 ├── values.yaml
 ├── Chart.yaml
```

**•	templates/**: Contains Kubernetes YAML files for Deployments, Services, StatefulSets, and Ingress.

**•	values.yaml:** Defines environment-specific values.

**•	Chart.yaml:** Stores metadata about the Helm chart.

**Deploying Helm Chart on AKS**

First, create a namespace for the deployment:

```sh
kubectl create namespace robot-shop
```

Then, install the Helm chart:

```sh
helm install roboshop helm/ -n robot-shop
```

Verify the deployment:

```sh
kubectl get pods -n robot-shop
```

**Step 4: Setting Up Ingress for External Access**

**Ingress Configuration**

We expose the web frontend using **Azure Application Gateway Ingress Controller:**

```sh
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: roboshop-ingress
  namespace: robot-shop
spec:
  ingressClassName: azure-application-gateway
  rules:
    - host: roboshop.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web
                port:
                  number: 8080
```

Apply the Ingress configuration:

```sh
kubectl apply -f ingress.yaml -n robot-shop
```

Once the **Ingress Controller** is ready, retrieve the public IP address:

```sh
kubectl get ingress -n robot-shop
```

Access the application in a browser using the IP or configured domain.

**Step 5: Managing Persistent Storage (Databases & Redis)**

**Using Persistent Volume Claims (PVCs) in AKS**

We use **Azure Disks** for persistent storage:

```sh
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: redis-pvc
  namespace: robot-shop
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: managed-csi
```

This ensures **Redis** data persists even if the pod restarts.

**Best Practices for Production Deployments**

**1.	Use Helm for Configuration Management**

o	Simplifies deployment across multiple environments (Dev, Staging, Prod).

**2.	Leverage Kubernetes StatefulSets for Databases**

o	Ensures data persistence and scaling for services like MongoDB and MySQL.

**3.	Configure Auto-Scaling for Microservices**

o	Use **Horizontal Pod Autoscaler (HPA):**

```sh
kubectl autoscale deployment cart --cpu-percent=70 --min=1 --max=5
```

**4.	Secure Ingress with TLS Certificates**

o	Use **cert-manager** to issue **Let's Encrypt** certificates for secure HTTPS access.

**5.	Enable Monitoring and Logging**

o	Use **Prometheus & Grafana** for monitoring and **Fluentd** for logging.

**Conclusion**

Deploying a **microservices-based e-commerce application** on **Azure Kubernetes Service (AKS)** requires a combination of **containerization, Kubernetes orchestration, persistent storage, and Ingress configuration.**

By following **best practices** like **Helm charts, StatefulSets, auto-scaling, and TLS-secured Ingress**, we ensure a **scalable, reliable, and production-ready deployment.**

**Next Steps**

•	Explore **Azure DevOps CI/CD** to automate deployments.

•	Implement **Prometheus & Grafana** for real-time monitoring.

•	Secure microservices with **Istio Service Mesh.**

