# Kubernetes Secrets: Command Reference Guide
### Project content table
- [Section 1: Core Project Workflow](#section-1-core-project-workflow)
- [Section 2: Advanced Operations](#section-2-advanced-operations)
- [Section 3: Production Guide](#section-3-production-guide)

> **Author**: [Md Toriqul Islam](https://linkedin.com/in/thetoriqul)  
> **Description**: Complete command reference for implementing Kubernetes Secrets with volume mounting  
> **Note**: Review all commands carefully before execution in your environment

## Section 1: Core Project Workflow

### Step 1: Create Secret from Literal Values
```bash
# Create secret
kubectl create secret generic basic-auth \
    --from-literal=username=super \
    --from-literal=password=my-s8cr3t

# Verify secret creation
kubectl get secret basic-auth

# Check secret details
kubectl describe secret basic-auth
```

### Step 2: Create Secret from Base64 Values
```bash
# Generate base64 encoded values
echo -n 'super' | base64
echo -n 'my-s8cr3t' | base64

# Apply secret from file
kubectl apply -f secret.yaml

# Verify secret creation
kubectl get secret basic-auth -o yaml
```

### Step 3: Deploy Application
```bash
# Apply the deployment
kubectl apply -f nginx-deploy-secret.yaml

# Verify deployment status
kubectl get deployments

# Check pods status
kubectl get pods

# Describe deployment
kubectl describe deployment nginx-deployment
```

### Step 4: Verify Secret Mounting
```bash
# Get pod name
POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')

# Access pod shell
kubectl exec -it $POD_NAME -- /bin/bash

# View mounted files
ls -l /etc/secret

# View secret contents
cat /etc/secret/username
cat /etc/secret/password

# Exit pod shell
exit
```

### Final Step: Cleanup
```bash
# Remove deployment
kubectl delete deployment nginx-deployment

# Delete secret
kubectl delete secret basic-auth

# Verify cleanup
kubectl get all
kubectl get secrets
```

## Section 2: Advanced Operations

### Secret Management
```bash
# View decoded secret values
kubectl get secret basic-auth -o jsonpath='{.data.username}' | base64 --decode
kubectl get secret basic-auth -o jsonpath='{.data.password}' | base64 --decode

# Update existing secret
kubectl delete secret basic-auth
kubectl create secret generic basic-auth \
    --from-literal=username=new-user \
    --from-literal=password=new-password

# Export secret
kubectl get secret basic-auth -o yaml > secret-backup.yaml
```

### Deployment Operations
```bash
# Scale deployment
kubectl scale deployment nginx-deployment --replicas=5

# Check rollout status
kubectl rollout status deployment/nginx-deployment

# View deployment details
kubectl describe deployment nginx-deployment

# Check pod logs
kubectl logs -l app=nginx
```

## Section 3: Production Guide

### Production Setup
```bash
# Create namespace
kubectl create namespace prod-secrets

# Create secret in namespace
kubectl -n prod-secrets create secret generic basic-auth \
    --from-literal=username=prod-user \
    --from-literal=password=prod-password

# Apply deployment to namespace
kubectl -n prod-secrets apply -f nginx-deploy-secret.yaml
```

### Monitoring
```bash
# Watch pod status
kubectl -n prod-secrets get pods -w

# View events
kubectl -n prod-secrets get events --sort-by=.metadata.creationTimestamp

# Check pod details
kubectl -n prod-secrets describe pod $(kubectl -n prod-secrets get pods -o name | head -1)
```

### Backup Operations
```bash
# Export all secrets
kubectl get secrets --all-namespaces -o yaml > secrets-backup.yaml

# Export all deployments
kubectl get deployments --all-namespaces -o yaml > deployments-backup.yaml
```

## Learning Notes

1. Verify secret creation before deploying applications
2. Always check pod status after deployment
3. Use namespaces for environment isolation
4. Monitor events for troubleshooting
5. Maintain regular backups of configurations

---

> 💡 **Best Practice**: Regularly rotate secrets in production environments

> ⚠️ **Warning**: Never store sensitive data in plain text

> 📝 **Note**: Always use proper namespacing in production