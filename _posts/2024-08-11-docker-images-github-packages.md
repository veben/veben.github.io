---
title: "Publishing Docker Images to Github Packages"
date: 2024-08-11
tags:
  - docker
  - github
  - github-packages
---

Publishing Docker images on **GitHub Packages** is an excellent free alternative to DockerHub or private repositories. In this guide, we'll demonstrate how to publish a Docker image using a Logger created in Go, which was previously used in [this post](https://veben.github.io/splunk-uf/) to send random logs to Splunk.

The Logger's repository is available [here](https://github.com/veben/go_random_logger)

## Prerequisites
**GitHub Account**: You need a GitHub account to host a Docker image in GitHub Packages.

## Hosting the GitHub Package
We'll use the Logger's repository to host the GitHub Package that will contain the Logger's Docker image.

## Creating the Dockerfile and Docker Compose File
Below is a classic `Dockerfile` for a Docker image based on a Go script:
```Dockerfile
FROM golang:latest

ARG GITHUB_USERNAME

LABEL org.opencontainers.image.authors="${GITHUB_USERNAME}"
LABEL org.opencontainers.image.source="https://github.com/${GITHUB_USERNAME}/go_random_logger"

WORKDIR /app
COPY . .
RUN go mod tidy && go build -o go_random_logger .
CMD ["./go_random_logger"]
```

- The `LABEL` instructions specify the author and repository for the Docker image.
- The `GITHUB_USERNAME` is passed as an environment variable.

Below is the `docker-compose.yml` file:
```yml
services:
  go_random_logger:
    build:
      context: .
      args:
        - GITHUB_USERNAME=${GITHUB_USERNAME}
    image: $REGISTRY/$GITHUB_USERNAME/go_random_logger:$TAG_VERSION
    volumes:
      - /var/log:/var/log
```

- The image path is defined using environment variables.

## Building and Pushing to Github Packages

### Method 1: Manual Process
Follow these steps to manually push the Logger's Docker image to GitHub Packages:

#### 1. Generate a Personal Access Token
To authenticate with GitHub Packages, you need to create a personal access token.

- Open your GitHub account, go to Settings -> Developer Settings -> [Personal access tokens](https://github.com/settings/tokens)
- Click **Generate new Token**
- Give it a name, select **write:packages** and save the token
- Define a `.env` file with your GitHub username and access token:
```text
GITHUB_USERNAME=<your_username>
GITHUB_TOKEN=<your_token>
```

#### 2. Define Registry Address and Increment Logger Version
- Update the tag version in the `.env` file, replacing `<version>` with your new version
- Specify the GitHub Container Registry domain:
```text
REGISTRY=ghcr.io
TAG_VERSION=<version>
```

#### 3. Load Environment Variables
- Load the environment variables from the `.env` file
```sh
source .env
```

#### 4. (Optional) Create a New Tag
Create and push a new tag to your repository:
```sh
git tag $TAG_VERSION
git push origin $TAG_VERSION
```

#### 5. Build the Docker Image
Build the Docker image using Docker Compose:
```sh
docker-compose build
```

#### 6. Push the Docker Image
- Log in to GitHub Packages
```sh
echo $GITHUB_TOKEN | docker login $REGISTRY -u $GITHUB_USERNAME --password-stdin
```
- Push the Docker image using Docker Compose:
```sh
docker-compose push
```

### Method 2: Automating with GitHub Actions
To automate the process of building and publishing a Docker image to GitHub Packages, you can use GitHub Actions. Follow these steps to set up the workflow:

Create a `.github/workflows/publish.yml` file with the following content. This file outlines the steps required to build the Docker image and publish it to Docker Packages:

```yml
name: Publish Docker Image

on:
  push:
    branches:
      - main

env:
  REGISTRY: ghcr.io
  GITHUB_USERNAME: veben
  TAG_VERSION: 1

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    permissions:
      contents: write    # Required to push tags and create new releases
      packages: write    # Required to push Docker images

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Create and push Git tag
        run: |
          git tag ${{ env.TAG_VERSION }}
          git push origin ${{ env.TAG_VERSION }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Install Docker Compose
        run: |
          sudo apt-get update
          sudo apt-get install -y docker-compose

      - name: Build and push Docker image via docker-compose
        run: |
          docker-compose build
          docker-compose push
```

#### Workflow Steps Explained
1. **Checkout Repository**: Uses the `actions/checkout@v3` action to clone the repository.
2. **Create and Push Git Tag**: Creates a new Git tag and pushes it to the repository.
3. **Log in to GitHub Container Registry**: Authenticates with the GitHub Container Registry using the `docker/login-action@v2`.
4. **Install Docker Compose**: Installs Docker Compose on the runner.
5. **Build and Push Docker Image**: Builds and pushes the Docker image using Docker Compose.

#### Environment Variables
The environment variables are defined directly in the workflow file instead of a `.env` file:
```yaml
env:
  REGISTRY: ghcr.io
  GITHUB_USERNAME: veben
  TAG_VERSION: 1
```

#### Triggering the Workflow
Push or merge your changes to the **main** branch. This action will trigger the GitHub Actions workflow to build and publish the new version of the Docker image.