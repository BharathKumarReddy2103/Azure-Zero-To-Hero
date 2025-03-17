**Integrating Azure Key Vault with Azure Kubernetes Service (AKS)**

**Introduction**

Managing sensitive information such as passwords, API tokens, and database credentials securely is a crucial aspect of DevOps and cloud security. **Azure Key Vault (AKV)** is a centralized secrets management solution that ensures compliance, security, and access control.

This guide provides a **step-by-step implementation of integrating Azure Key Vault with an Azure Kubernetes Service (AKS) cluster** using the **Secret Store CSI Driver**. You will learn how to:

•	Store and retrieve secrets securely from **Azure Key Vault.**

•	Configure **Secret Store CSI Driver in AKS.**

•	Use **Managed Identities** to authenticate Kubernetes pods with Key Vault.

•	Implement best practices for secure secret management in **Kubernetes.**

Let’s dive into the details and practical implementation.

---

**Why Use Azure Key Vault Instead of Kubernetes Secrets?**

Many DevOps tools like **Kubernetes Secrets, GitHub Actions, and GitLab CI/CD** provide built-in secret management. However, **Azure Key Vault offers several advantages:**

**1.	Centralized Management** – Store all secrets in one place, reducing complexity.

**2.	Automated Secret Rotation** – Comply with security policies requiring periodic updates.

**3.	Role-Based Access Control (RBAC)** – Granular access permissions for applications.

**4.	Seamless Integration with Managed Identities** – Secure authentication without storing credentials in Kubernetes.

**5.	Compliance & Security** – Organizations with strict security policies require centralized secrets management.

---

**Solution Architecture**

The following architecture demonstrates how an **AKS pod** securely retrieves secrets from **Azure Key Vault** using the **Secret Store CSI Driver.**

**1.	Azure Key Vault** stores sensitive data (e.g., API keys, passwords).

**2.	Managed Identity** is assigned to AKS to enable authentication.

**3.	Secret Store CSI Driver** is deployed in the AKS cluster.

**4.	Kubernetes Pod** fetches secrets from **Azure Key Vault** securely.

```sh
graph LR;
    AKS_Pod -- Uses --> Secret_Store_CSI_Driver;
    Secret_Store_CSI_Driver -- Connects --> Azure_Key_Vault;
    Azure_Key_Vault -- Grants Access --> Managed_Identity;
    Managed_Identity -- Authenticates --> AKS_Pod;
```

---

**Prerequisites**

Before starting, ensure you have:

**•	Azure CLI installed** – Install Guide (https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)

**•	kubectl installed** – Install Guide (https://kubernetes.io/docs/tasks/tools/)

**•	Azure subscription** with necessary permissions

**•	An existing AKS cluster** or create a new one

---

**Step-by-Step Implementation**

**Step 1: Create an Azure Key Vault**

Create an Azure Key Vault and store a **secret** and **key.**

```sh
az group create --name keyvault-demo --location eastus

az keyvault create --name aks-demo-kv \
  --resource-group keyvault-demo \
  --location eastus \
  --enable-rbac-authorization
```

Add a **secret** to the Key Vault:

```sh
az keyvault secret set --vault-name aks-demo-kv \
  --name my-secret --value "super-secret-password"
```

Add a **key** to the Key Vault:

```sh
az keyvault key create --vault-name aks-demo-kv \
  --name my-key --protection software
```

**Step 2: Enable Secret Store CSI Driver in AKS**

Create an **AKS cluster** with the **Secret Store CSI Driver** and **OIDC issuer** enabled.

```sh
az aks create --resource-group keyvault-demo \
  --name aks-keyvault-cluster \
  --node-count 1 \
  --enable-addons azure-keyvault-secrets-provider \
  --enable-oidc-issuer
```

**Step 3: Create a Managed Identity**

Create a **Managed Identity** to allow AKS to authenticate with **Azure Key Vault.**

```sh
az identity create --name aks-keyvault-mi \
  --resource-group keyvault-demo
```

Extract the **client ID and tenant ID** of the **Managed Identity:**

```sh
export MI_CLIENT_ID=$(az identity show --name aks-keyvault-mi --resource-group keyvault-demo --query clientId -o tsv)
export MI_TENANT_ID=$(az identity show --name aks-keyvault-mi --resource-group keyvault-demo --query tenantId -o tsv)
```

**Step 4: Assign Key Vault Access to Managed Identity**

Grant the **Key Vault Administrator role** to the Managed Identity.

```sh
az role assignment create --role "Key Vault Administrator" \
  --assignee $MI_CLIENT_ID \
  --scope $(az keyvault show --name aks-demo-kv --query id -o tsv)
```

**Step 5: Configure Kubernetes Service Account**

Create a **Kubernetes Service Account** and associate it with the Managed Identity.

```sh
kubectl create namespace keyvault-ns

kubectl create serviceaccount keyvault-sa --namespace keyvault-ns
```

Bind the **Service Account** to the **Managed Identity** using **OIDC Federation.**

```sh
az identity federated-credential create \
  --name aks-keyvault-oidc \
  --identity-name aks-keyvault-mi \
  --resource-group keyvault-demo \
  --issuer $(az aks show --resource-group keyvault-demo --name aks-keyvault-cluster --query identityProfile.kubeletidentity.issuer -o tsv) \
  --subject system:serviceaccount:keyvault-ns:keyvault-sa
```

**Step 6: Deploy Secret Store CSI Driver Configuration**

Create a **Secret Provider Class** to specify the Key Vault integration.

```sh
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: keyvault-provider
  namespace: keyvault-ns
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"
    userAssignedIdentityID: "<MI_CLIENT_ID>"
    keyvaultName: aks-demo-kv
    tenantId: "<MI_TENANT_ID>"
    objects: |
      array:
        - |
          objectName: my-secret
          objectType: secret
          objectVersion: ""
        - |
          objectName: my-key
          objectType: key
          objectVersion: ""
```

Apply the configuration:

```sh
kubectl apply -f secret-provider-class.yaml
```

**Step 7: Deploy a Test Kubernetes Pod**

Create a test **pod** to verify if the secret is mounted as a volume.

```sh
apiVersion: v1
kind: Pod
metadata:
  name: keyvault-test-pod
  namespace: keyvault-ns
spec:
  serviceAccountName: keyvault-sa
  containers:
    - name: busybox
      image: busybox
      command: [ "sleep", "3600" ]
      volumeMounts:
        - name: secrets-store
          mountPath: "/mnt/secrets"
          readOnly: true
  volumes:
    - name: secrets-store
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: keyvault-provider
```

Deploy the pod:

```sh
kubectl apply -f keyvault-test-pod.yaml
```

Verify if the secret is successfully mounted:

```sh
kubectl exec -n keyvault-ns keyvault-test-pod -- cat /mnt/secrets/my-secret
```

---

**Conclusion**

By integrating **Azure Key Vault** with **AKS**, you can enhance **security, compliance, and centralized secret management**. Using **Secret Store CSI Driver** and **Managed Identities**, we eliminate the need to store secrets directly in Kubernetes, reducing security risks.

**Next Steps**

•	Implement **Azure Key Vault with CI/CD Pipelines** (GitHub Actions, GitLab CI/CD).

•	Explore **RBAC policies** for more granular access control.

•	Automate **secret rotation policies** using Azure Key Vault.

Would you like to **contribute to this repository?** Feel free to **open an issue or PR**
