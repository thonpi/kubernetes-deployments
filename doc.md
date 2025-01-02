## Build and Use Local Docker Image with Minikube (for Development Only)
1. Configure Docker to use Minikube’s Docker Daemon: Run the following command to set your local Docker environment to use Minikube’s Docker daemon:
`eval $(minikube docker-env)`
This makes the local Docker CLI use Minikube’s Docker daemon, meaning any images you build will be accessible within your Minikube cluster.

2. Build your Docker image in Minikube: Build the image just like you would normally in Docker:
`docker build -t my-image:latest .`

3. Update your deployment.yaml: In your deployment.yaml, set the image field to use the locally built image (since you’re using Minikube, you can just refer to it as my-image:latest without a registry URL).

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: api-app
  template:
    metadata:
      labels:
        app: api-app
    spec:
      containers:
      - name: api-app
        image: my-image:latest  # No registry URL needed
        ports:
        - containerPort: 3000
```
Deploy the updated manifests: Once your image is built, deploy it via Argo CD:
`argocd app sync up-web-api`



## To start running service in kubernetes cluster
1. Create service and get url
   `kubectl expose deployment ${{app-name}} --type=NodePort --port=3000`
2. If already exist, just do port-forward
   `kubectl port-forward svc/${{serviceORapp-name}} 3000:3000`

## ArgoCD

##### Install Argo CD using helm or directly through kubectl manifests:

- `helm repo add argo https://argoproj.github.io/argo-helm`
- `helm install argocd argo/argo-cd --namespace argocd --create-namespace`

##### Port-forward the Argo CD server for accessing the UI:

`kubectl port-forward svc/argocd-server -n argocd 8080:443`

Access Argo CD UI via: http://localhost:8080 and log in using:
- username: `admin`
- password: run this to get `kubectl get secret argocd-initial-admin-secret -n argocd -o=jsonpath='{.data.password}' | base64 --decode`.


##### Connecting Argo CD to a Git Repository
Access the Argo CD UI and go to Settings > Repositories.