Here are the complete steps to create a **Jenkins job** that:

1. Clones the repo: `https://github.com/saddammoh941/Python-app.git`
2. Builds a Docker image
3. Pushes the image to Docker Hub

---

## Prerequisites

Before creating the job, ensure:

* Jenkins has the **Docker CLI** installed and the **Docker daemon** is running.
* Jenkins has **Docker credentials** for Docker Hub.
* Jenkins has **Git** installed.
* Jenkins user has permission to run Docker commands (`sudo` may be needed or add Jenkins user to `docker` group).

---

## 🔧 Step-by-Step Jenkins Job Setup

### 1. Create a new Jenkins job

* Go to **Jenkins Dashboard**
* Click **New Item**
* Name it: `Python-Docker-Build`
* Select **Freestyle project**
* Click **OK**

---

### 2. Configure Git Source

Under **Source Code Management**:

* Select **Git**
* Set Repository URL:

  ```
  https://github.com/saddammohd941/Python-app.git
  ```
* Leave credentials empty if public repo.

---

### 3. Configure Build Environment (Optional)

* Check **"Provide Node & Label"** if needed.
* (Optional) Add build environment variables.

---

### 4. Add Docker Hub Credentials

Under **Manage Jenkins > Credentials > Global > Add Credentials**:

* Kind: **Username with password**
* Username: your Docker Hub username
* Password: your Docker Hub password or access token
* ID: `docker-hub-creds` (you’ll use this in the pipeline)

---

### 5. Add Build Steps

Go to **Build > Add build step > Execute shell**
Paste the following script (replace Docker Hub username accordingly):

```bash
#!/bin/bash

# Set variables
IMAGE_NAME=yourdockerhubusername/python-app
IMAGE_TAG=latest

# Log in to Docker Hub (using stored Jenkins credentials)
echo "$DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USERNAME" --password-stdin

# Build the Docker image
docker build -t $IMAGE_NAME:$IMAGE_TAG .

# Push the image
docker push $IMAGE_NAME:$IMAGE_TAG
```

```bash
#!/bin/bash
# set -e  # Exit immediately on error

# Variables
IMAGE_NAME="$DOCKER_HUB_USERNAME/python-app"
TAG="latest"
FULL_IMAGE_NAME="$IMAGE_NAME:$TAG"

echo "Building Docker image: $FULL_IMAGE_NAME"

# Confirm credentials are passed
if [ -z "$DOCKER_HUB_USERNAME" ] || [ -z "$DOCKER_HUB_PASSWORD" ]; then
  echo "Docker Hub credentials are not set. Aborting."
  exit 1
fi

echo "Logging into Docker Hub as $DOCKER_HUB_USERNAME..."
echo "$DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USERNAME" --password-stdin

# Build Docker image
docker build -t "$FULL_IMAGE_NAME" .

# Push Docker image
echo "Pushing image to Docker Hub..."
docker push "$FULL_IMAGE_NAME"

echo "Image pushed successfully: $FULL_IMAGE_NAME"
```

---

### 6. Inject Docker Hub Credentials into Shell

Use **"Use secret text(s) or file(s)"** in the **Build Environment** section:

* Add two **"Username and password (separated)"** bindings:

  * Variable: `DOCKER_HUB_USERNAME`
  * Variable: `DOCKER_HUB_PASSWORD`
  * Credentials: `docker-hub-creds`

---

### 7. Save and Build

* Click **Save**
* Click **Build Now**

---

### Output

If everything is configured correctly, the Jenkins console log should show:

* Cloning the Git repo
* Docker image being built
* Successful push to Docker Hub

---
