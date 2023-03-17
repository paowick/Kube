# Kube

## Install kubectl and minikube

1. install kubectl
    use **curl** to insatll
   ``` 
   curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe" ```
   ```
    Test command
   ```
   kubectl version --client
   ```
2. insatll minikube
    Download and run the install
    ```
    New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
    Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
    ```
    Add the minikube.exe binary to your PATH (must be run as Administrator)
    ```
    $oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
    if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
    [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
    }
    ```
    Test command
    ```
    Test command
    ```
    #### Start minikube
    ```
    minikube start
    ```
## Install traefik
1. Install Traefik Resource Definitions
    ```
    kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-definition-v1.yml
    ```
2. Install RBAC for Traefik
   ```
   kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-rbac.yml
   ```
3. Install Traefik Helmchart
    Must be install helm [here](https://get.helm.sh/helm-v3.11.2-windows-amd64.zip) and add the helm.exe binary to your **PATH ENVIRONMENT**
    ```
    helm repo add traefik https://traefik.github.io/charts 
    helm repo update 
    helm install traefik traefik/traefik 
    ```
4. Verify service is running
    ```
    kubectl get svc -l app.kubernetes.io/name=traefik
    kubectl get po -l app.kubernetes.io/name=traefik
    ```
##  create secrete
```
htpasswd -nB user | tee auth-secret
# New password:
# Re-type new password:
# Example output user:$2y$05$W4zCVrqGg8wKtIjOAU.gGu8MQC9k7sH4Wd1v238UfiVuGkf0xfDUu
```
## Dry run to create a secret deployment.
```
kubectl create secret generic -n traefik dashboard-auth-secret --from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
```
This will automatic create a deployment file dashboard-secret.yaml
Copy users secret from dashboard-secret.yaml and replace in traefik-dashboard.yaml

## Deploy
```
kubectl apply -f . 
```

## Result