# Deploy Kubernetes Applications on Google Cloud: Challenge Lab

```
export REPO="valkyrie-docker"       # Repository Name
export IMAGE="valkyrie-dev"         # Docker Image
export TAG="v0.0.1"                 # Tag Name
export REGION="us-east1"            # Region
export ZONE="us-east1-c"            # Zone
export CLUSTER="valkyrie-dev"       # Cluster name
```



## TASK 1
```
echo "=== TASK 1: Downloading source & building Docker image ==="
source <(gcloud storage cat gs://spls/gsp318/script.sh)
gcloud storage cp gs://spls/gsp318/valkyrie-app.tgz .
tar -xzf valkyrie-app.tgz
cd valkyrie-app

cat << 'EOF' > Dockerfile
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF

docker build -t IMAGE:{TAG} .
````







```
echo "=== TASK 2: Testing Docker container locally ==="
docker run -d -p 8080:8080 IMAGE:{TAG}



echo "=== TASK 3: Creating Artifact Registry & Pushing Image ==="
gcloud artifacts repositories create ${REPO} \
    --repository-format=docker \
    --location=${REGION} \
    --description="Valkyrie Docker repository"

gcloud auth configure-docker ${REGION}-docker.pkg.dev
docker tag IMAGE:{TAG} REGION-docker.pkg.dev/DEVSHELL_PROJECT_ID/REPO/{IMAGE}:${TAG}
docker push REGION-docker.pkg.dev/DEVSHELL_PROJECT_ID/REPO/{IMAGE}:${TAG}

````


## TASK 4

```

echo "=== TASK 4: Deploying to Kubernetes Cluster ==="
gcloud container clusters get-credentials ${CLUSTER} --zone ${ZONE}

cd k8s
# Automatically update the deployment.yaml placeholder with your exact project and image path
sed -i.bak "s#us-east1-docker.pkg.dev/PROJECT-ID/REPOSITORY/IMAGE:TAG#REGION-docker.pkg.dev/DEVSHELL_PROJECT_ID/REPO/{IMAGE}:${TAG}#g" deployment.yaml

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

echo "=== ALL TASKS COMPLETED SUCCESSFULLY! ==="

````
                                        FOR TASK 3(ERROR)

````
export REPO="valkyrie-docker"
export IMAGE="valkyrie-dev"
export TAG="v0.0.1"
export REGION="us-east1"
```

```
cd ~/valkyrie-app
docker build -t ${IMAGE}:${TAG} .
```



docker tag IMAGE:{TAG} REGION-docker.pkg.dev/DEVSHELL_PROJECT_ID/REPO/{IMAGE}:${TAG}
gcloud auth configure-docker ${REGION}-docker.pkg.dev
docker push REGION-docker.pkg.dev/DEVSHELL_PROJECT_ID/REPO/{IMAGE}:${TAG}


