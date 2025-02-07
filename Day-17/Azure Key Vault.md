**Azure Key Vault**

**1. What is Azure Key Vault?**

Azure Key Vault is a cloud-based secrets management service that helps securely store and manage secrets, keys, certificates, and passwords used by applications and services. It provides centralized security, access control, and auditing for sensitive information.

**Key Features of Azure Key Vault**

✅ **Secrets Management** → Store API keys, connection strings, and passwords securely.

✅ **Key Management** → Generate, store, and manage encryption keys (RSA, AES).

✅ **Certificate Management** → Securely manage SSL/TLS certificates for web applications.

✅ **Access Control & Auditing** → Uses Role-Based Access Control (RBAC) and logs access using Azure Monitor and Log Analytics.

✅ **Integration with Azure Services** → Works with Azure Kubernetes Service (AKS), Azure Functions, Virtual Machines, and Azure DevOps.

---

**2. How Azure Key Vault Works?**

Azure Key Vault works by storing secrets, encryption keys, and certificates and providing secure access through authentication mechanisms like Azure Active Directory (Azure AD).

**Azure Key Vault Workflow**

1️⃣ **Create a Key Vault** in Azure.

2️⃣ **Store secrets, keys, or certificates** securely.

3️⃣ **Grant access** using **RBAC or Managed Identity.**

4️⃣ **Applications retrieve secrets** using **Azure SDK, REST API, or Kubernetes Secrets Store CSI Driver.**

---

**3. How to Do Secret Management with Azure Key Vault?**

**Step 1: Create an Azure Key Vault**

```bash
az keyvault create --name MyKeyVault --resource-group MyResourceGroup --location eastus
```

**Step 2: Store a Secret in Key Vault**

```bash
az keyvault secret set --vault-name MyKeyVault --name "DBPassword" --value "SuperSecurePassword123!"
```

**Step 3: Retrieve the Secret**

```bash
az keyvault secret show --vault-name MyKeyVault --name "DBPassword"
```

**Step 4: Use Key Vault with an Azure Application**

   - Configure Managed Identity for your application.

   - Retrieve secrets using Azure SDK or REST API.

---

**4. Security Best Practices for Azure Key Vault**

- Use Azure AD for Authentication

- Never store keys in **config files**.

- Use Managed Identity instead of embedding secrets in code.

- **Restrict Access with RBAC & Policies**

- Assign least privilege access using Azure Role-Based Access Control (RBAC).

- Use Access Policies to control who can read/write secrets.

- Enable Soft Delete & Purge Protection

- Prevent accidental deletion of secrets using soft delete.

- Enable Purge Protection to avoid permanent data loss.

- **Enable Logging & Monitoring**

- Monitor Key Vault access logs with Azure Monitor & Log Analytics.

- Enable Azure Defender for security alerts.

- **Use Key Rotation & Expiry Policies**

- Regularly rotate secrets and set expiration policies.

---

**5. How to Integrate Azure Key Vault with AKS (Azure Kubernetes Service)?**

Azure Key Vault can be integrated with AKS (Azure Kubernetes Service) using the Secrets Store CSI Driver, which allows Kubernetes applications to access secrets without storing them as Kubernetes Secrets.

🛠 **Real-World Project: Integrate Azure Key Vault with AKS using Secrets Store CSI Driver**

**Project Goal**: Deploy a microservice in AKS that securely retrieves secrets from Azure Key Vault using the Secrets Store CSI Driver instead of hardcoded Kubernetes secrets.

---

**Step 1: Install Azure Key Vault Provider for CSI Driver in AKS**

```bash
az aks enable-addons --resource-group MyResourceGroup --name MyAKSCluster --addons azure-keyvault-secrets-provider
```

**Step 2: Create an Azure Key Vault**

```bash
az keyvault create --name MyKeyVault --resource-group MyResourceGroup --location eastus
```

**Step 3: Store Secrets in Azure Key Vault**

```bash
az keyvault secret set --vault-name MyKeyVault --name "DB_CONNECTION_STRING" --value "Server=mydb.database.windows.net;User Id=admin;Password=SecurePass123"
```

**Step 4: Assign Managed Identity to AKS Node Pool**

```bash
az aks update --resource-group MyResourceGroup --name MyAKSCluster --enable-managed-identity
```

Retrieve the Managed Identity Client ID:

```bash
CLIENT_ID=$(az aks show --resource-group MyResourceGroup --name MyAKSCluster --query "identityProfile.kubeletidentity.clientId" -o tsv)
```

**Step 5: Grant AKS Permission to Access Key Vault**

```bash
az keyvault set-policy --name MyKeyVault --spn $CLIENT_ID --secret-permissions get list
```

---

**Step 6: Deploy Kubernetes Secret Store Configuration**

Create a file secret-provider.yaml:

```bash
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: my-keyvault-secrets
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    keyvaultName: "MyKeyVault"
    objects: |
      array:
        - objectName: "DB_CONNECTION_STRING"
          objectType: "secret"
    tenantId: "your-tenant-id"
```

Apply the Secret Provider Class:

```bash
kubectl apply -f secret-provider.yaml
```

---

**Step 7: Deploy Application Using CSI Secrets**

Create a Deployment YAML (deployment.yaml):

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: myacr.azurecr.io/my-app:v1
          env:
            - name: DB_CONNECTION_STRING
              valueFrom:
                secretKeyRef:
                  name: my-keyvault-secrets
                  key: DB_CONNECTION_STRING
```

Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

---

**6. How Does This Work?**

   - The Secrets Store CSI Driver dynamically mounts Azure Key Vault secrets into the Kubernetes pod at runtime.

   - The application retrieves secrets without storing them as Kubernetes Secrets.

   - When the secret in Key Vault updates, it automatically updates inside the pod.

   - This method enhances security by eliminating hardcoded secrets in Kubernetes YAML files.

---

**7. Benefits of Using Azure Key Vault with AKS**

✅ No Hardcoded Secrets in Kubernetes → Eliminates risks of exposing credentials.

✅ Automatic Secret Rotation → Secrets are updated dynamically in running pods.

✅ Seamless Authentication → Uses Azure Managed Identity for access.

✅ Better Security & Compliance → All secrets are stored centrally in Key Vault, reducing exposure risks.

---

**8. Conclusion**

Azure Key Vault is a powerful security tool for managing secrets, encryption keys, and certificates securely. Integrating Key Vault with AKS using the Secrets Store CSI Driver improves security, automation, and compliance in Kubernetes workloads.
