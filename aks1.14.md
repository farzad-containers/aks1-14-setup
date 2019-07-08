az group create --name frz-rg-aks-5 --location westeurope

{
  "id": "/subscriptions/702c550c-aeab-400d-b4c0-4519450f274d/resourceGroups/frz-rg-aks-5",
  "location": "westeurope",
  "managedBy": null,
  "name": "frz-rg-aks-5",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}

PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group frz-rg-aks-5 \
    --name frz-aks-5 \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
    
    
az aks nodepool add \
    --resource-group frz-rg-aks-5 \
    --cluster-name frz-aks-5 \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0    



az aks get-credentials --resource-group frz-rg-aks-5 --name frz-aks-5




#Example of yaml file:



apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample




