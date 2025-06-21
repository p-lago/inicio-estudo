# Deployment Guide - React App to OCI Harbor

This guide explains how to deploy your React TypeScript application to an OCI Harbor registry using GitHub Actions.

## Prerequisites

1. **OCI Harbor Registry**: You need access to an OCI Harbor instance
2. **GitHub Repository**: Your code should be in a GitHub repository
3. **Docker**: The workflow uses Docker to build and push images

## Setup Instructions

### 1. GitHub Secrets Configuration

Add the following secrets to your GitHub repository:

1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Add the following repository secrets:

```
HARBOR_REGISTRY=your-harbor-instance.com
HARBOR_USERNAME=your-username
HARBOR_PASSWORD=your-password
HARBOR_IMAGE_NAME=your-project/react-app
```

### 2. Harbor Registry Configuration

- **HARBOR_REGISTRY**: Your Harbor instance URL (e.g., `harbor.company.com`)
- **HARBOR_USERNAME**: Your Harbor username
- **HARBOR_PASSWORD**: Your Harbor password or token
- **HARBOR_IMAGE_NAME**: The image name in your Harbor project (e.g., `myproject/react-app`)

### 3. Workflow Explanation

The GitHub Actions workflow (`.github/workflows/deploy.yml`) does the following:

1. **Triggers**: Runs on pushes to main/master branch and pull requests
2. **Build**: Uses Docker Buildx for efficient multi-platform builds
3. **Authentication**: Logs into your Harbor registry
4. **Tagging**: Creates appropriate tags for the image
5. **Push**: Pushes the built image to Harbor
6. **Deployment**: Optionally deploys to Kubernetes (you can customize this)

### 4. Local Development

To test the Docker build locally:

```bash
# Build the image
docker build -t react-app .

# Run the container
docker run -p 8080:80 react-app

# Access the app at http://localhost:8080
```

### 5. Kubernetes Deployment

The `k8s/deployment.yaml` file provides a complete Kubernetes deployment:

```bash
# Apply the deployment (replace placeholders first)
kubectl apply -f k8s/deployment.yaml
```

**Important**: Update the following in the deployment file:
- Replace `${HARBOR_REGISTRY}` and `${HARBOR_IMAGE_NAME}` with actual values
- Update `your-domain.com` with your actual domain

### 6. Customization Options

#### Environment Variables
You can add environment variables to the Dockerfile or Kubernetes deployment for different environments.

#### Build Arguments
Add build arguments to the Dockerfile for different configurations:

```dockerfile
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV
```

#### Multi-stage Builds
The Dockerfile uses multi-stage builds to optimize the final image size.

### 7. Monitoring and Health Checks

The application includes:
- Health check endpoint at `/health`
- Nginx configuration with proper caching
- Security headers
- Gzip compression

### 8. Troubleshooting

#### Common Issues:

1. **Authentication Failed**: Check your Harbor credentials
2. **Build Fails**: Ensure all dependencies are in package.json
3. **Image Push Fails**: Verify Harbor registry URL and permissions
4. **Kubernetes Deployment Fails**: Check image pull secrets and namespace

#### Debug Commands:

```bash
# Check Docker build
docker build --no-cache -t react-app .

# Test Harbor login
docker login your-harbor-instance.com

# Check Kubernetes deployment
kubectl describe deployment react-app
kubectl logs -l app=react-app
```

## Security Best Practices

1. **Use Harbor Robot Accounts**: Instead of personal credentials
2. **Image Scanning**: Enable vulnerability scanning in Harbor
3. **Secrets Management**: Use Kubernetes secrets for sensitive data
4. **Network Policies**: Implement proper network policies in Kubernetes
5. **Regular Updates**: Keep base images and dependencies updated

## Next Steps

1. Set up your GitHub secrets
2. Push your code to trigger the first deployment
3. Configure your Kubernetes cluster
4. Set up monitoring and logging
5. Implement CI/CD for other environments (staging, production) 