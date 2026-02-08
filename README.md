# Kubernetes CI with Minikube and GitHub Actions (Python)

This project demonstrates a **simple Kubernetes automation pipeline** using:
- A Python Flask web application.
- A Docker image built from the app.
- Kubernetes Deployment and Service manifests.
- A GitHub Actions workflow that starts a **temporary Minikube cluster**, deploys the app, tests it, and shuts down automatically.

On every push to `main`, the pipeline gives you an end-to-end test of your container and Kubernetes configuration without using any cloud provider.

## Project overview

1. Code lives in a GitHub repository (Python + Flask).
2. A Docker image is built for the app.
3. GitHub Actions starts Minikube inside the CI runner.
4. The Docker image is built inside Minikube’s Docker environment.
5. Kubernetes manifests are applied to deploy the app.
6. A simple HTTP request checks that the app is reachable.
7. When the job finishes, the runner (and Minikube) are discarded.

## Key components (files)

- `app/` – Python source code for the Flask application.
- `k8s/` – Kubernetes manifests:
  - `deployment.yaml` – defines the Pods/Deployment for the app.
  - `service.yaml` – exposes the app inside the cluster (NodePort).
- `Dockerfile` – describes how to containerize the Flask app.
- `.github/workflows/ci.yaml` – GitHub Actions workflow:
  - Triggers on pushes and pull requests.
  - Starts Minikube, builds the Docker image, applies manifests, and runs a curl test.

> Note: Keep the filenames and folder structure consistent so the workflow can find everything.

## How the CI workflow works (high level)

When you push to `main`:

1. **Checkout** – The workflow checks out the repository.
2. **Start Minikube** – A Minikube cluster is started in the GitHub Actions runner.
3. **Build image** – The workflow configures Docker to use Minikube’s Docker daemon and builds the `python-hello:latest` image.
4. **Deploy to Kubernetes** – It applies the manifests in `k8s/` and waits for the Deployment rollout to complete.
5. **Test the service** – It uses `minikube service ... --url` to get the service URL and `curl` to verify the response.

The whole process is automatic and runs to completion without manual intervention.

## How to use this project

1. **Create a GitHub repository** and clone it locally.
2. Add the project files and folders, including:
   - `app/`, `k8s/`, `Dockerfile`, and `.github/workflows/ci.yaml`.
3. Commit and push to the `main` branch:

   ```bash
   git add .
   git commit -m "Initial Kubernetes CI project"
   git push -u origin main
   ```
4. Open your repository on GitHub and click the Actions tab.
5. Select the latest workflow run

> Every subsequent push to main will trigger the same automated build + deploy + test pipeline.