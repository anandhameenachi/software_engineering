# SOFTWARE ENGINEERING - DIGITAL ASSIGNMENT-1

## STEP 1: CREATING NODEJS APP

### Prerequisites
- **Docker Version:** (Check using `docker --version`)
- **Node.js Version:** (Check using `node -v`)

## STEP 2: CREATING DOCKERFILE

1. Create a `Dockerfile` in the project directory.
2. Add the following content:
   ```dockerfile
   FROM node:latest
   WORKDIR /app
   COPY . .
   RUN npm install
   CMD ["node", "app.js"]
   EXPOSE 3000
   ```

## STEP 3: BUILD IMAGE

### Open Node.js Project
```bash
ls  # Lists the contents of the directory
```

### Downloading the App Files
Ensure all necessary files are inside the directory.

### Create a Docker Folder
```bash
mkdir docker
mv Dockerfile docker/
```

### Build the Docker Image
```bash
docker build -t nodeapp .
```

### Test the Docker Image
```bash
docker run -p 3000:3000 nodeapp
```

### Get the Running Container's IP Address
```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <CONTAINER_ID>
```

### Access the Application
```bash
http://172.16.114.150:3000
```

### Remove Docker Container
```bash
docker rm -f <CONTAINER_ID>
```

## STEP 4: PUSH IMAGE TO DOCKER HUB

1. Login to Docker Hub
   ```bash
   docker login
   ```
2. Tag the Image
   ```bash
   docker tag nodeapp <your-dockerhub-username>/nodeapp:latest
   ```
3. Push the Image
   ```bash
   docker push <your-dockerhub-username>/nodeapp:latest
   ```

## STEP 5: KUBERNETES DEPLOYMENT YAML AND SERVICE YAML FILES

### Create `deployment.yml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodeapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodeapp
  template:
    metadata:
      labels:
        app: nodeapp
    spec:
      containers:
      - name: nodeapp
        image: <your-dockerhub-username>/nodeapp:latest
        ports:
        - containerPort: 3000
```

### Create `service.yml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeapp-service
spec:
  selector:
    app: nodeapp
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000
  type: NodePort
```

## STEP 6: DEPLOY NODEJS APPLICATION IN KUBERNETES

### Setup Kubernetes Cluster (Minikube)
```bash
minikube start
```

### Deploy the Application
```bash
kubectl apply -f deployment.yml
kubectl apply -f service.yml
```

### Get Deployment Status
```bash
kubectl get deployments
kubectl get pods
```

### Access the Application
```bash
kubectl get services
http://192.168.49.2:31110
```

## Testing Scenarios

### 1. Application Availability Tests
```bash
kubectl get services
curl http://<EXTERNAL-IP>:<PORT>
```
✅ Expected Output: Homepage or API response should be returned.

### 2. Scaling Tests
```bash
kubectl get hpa
kubectl run --rm -it --image=busybox stress-test -- /bin/sh
# Inside BusyBox shell:
while true; do wget -q -O- http://<SERVICE-IP>:<PORT>; done
```
✅ Expected Output: Number of pods should increase dynamically.

### 3. Rolling Update & Rollback Test
```bash
kubectl set image deployment/nodeapp-deployment nodeapp=newimage:v2
```
✅ Expected Output: New version is deployed with zero downtime.

Rollback:
```bash
kubectl rollout undo deployment/nodeapp-deployment
```

### 4. Pod Failure and Self-Healing Test
```bash
kubectl delete pod <POD_NAME>
```
✅ Expected Output: Kubernetes should recreate a new pod automatically.

### 5. Persistent Storage Test (If Implemented)
```bash
kubectl delete pod <POD_NAME>
kubectl get pods -w
```
✅ Expected Output: Data should persist after pod restart.

### 6. Logging Test
```bash
kubectl logs <POD_NAME>
```
✅ Expected Output: Application logs should be visible.

---

## 🎯 Conclusion
This guide provides a complete step-by-step approach to setting up, containerizing, deploying, and testing a Node.js application using **Docker and Kubernetes**. 🚀

