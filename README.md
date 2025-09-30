# devops-demo-Real Time Project
# training batch demo Real Time Project


# 🚀 CI/CD Deployment to AWS EKS with GitHub Actions + Ansible

This repository implements a **complete CI/CD pipeline** for deploying a containerised application to an **AWS EKS (Kubernetes) cluster**.
It uses:

* **GitHub Actions** for automation
* **Docker** for image build & push
* **Trivy** for security scanning
* **Ansible** for applying manifests to the cluster

---

## 📂 Repository Structure

```
.
├── Application/
│   ├── pom.xml                # Java/Maven project
│   └── src/                   # Application source code
│
├── Ansible/
│   ├── ansible_k8s_deploy_playbook.yaml  # Ansible playbook to deploy to EKS
│   └── k8s_deployment.yaml               # Kubernetes Deployment manifest
│
└── .github/
    └── workflows/deploy.yaml   # GitHub Actions pipeline
```

---

## 🔑 Prerequisites

* An existing **AWS EKS cluster** with worker nodes running
* **Docker Hub account** for hosting images
* GitHub repository with the following **Secrets** configured:

| Secret Name             | Purpose                                           |
| ----------------------- | ------------------------------------------------- |
| `AWS_ACCESS_KEY_ID`     | AWS IAM Access Key with EKS + ECR permissions     |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM Secret Key                                |
| `KUBECONFIG_DATA`       | Base64-encoded kubeconfig of your EKS cluster     |
| `DOCKERHUB`             | Docker Hub password or token                      |
| `GITHUB_TOKEN`          | Provided automatically by GitHub (no need to set) |

---

## ⚙️ Pipeline Flow

1. **Checkout code**
   GitHub Actions checks out the repo.

2. **Configure AWS credentials**
   Needed to talk to EKS.

3. **Configure kubeconfig**
   Decoded from `KUBECONFIG_DATA` and stored at `$HOME/.kube/config`.

4. **Build and scan Docker image**
   Maven builds JAR; Docker builds image; Trivy scans it.

5. **Push Docker image**
   Push to Docker Hub.

6. **Update Kubernetes manifest**
   `sed` replaces the image tag in `k8s_deployment.yaml`.

7. **Run Ansible playbook**
   Ansible runs `kubectl apply` to deploy to EKS.

---

##  GitHub Actions Workflow

`.github/workflows/ci-cd.yaml`:

```
https://github.com/jadalaramani/Ansible_k8s_gha/blob/main/.github/workflows/ci-cd.yaml
```

---

## 📝 Ansible Playbook

`Ansible/ansible_k8s_deploy_playbook.yaml`:

```
https://github.com/jadalaramani/Ansible_k8s_gha/blob/main/Ansible/ansible_k8s_deploy_playbook.yaml
```
---

## 📝 Kubernetes Deployment Manifest

`Ansible/k8s_deployment.yaml`:
```
https://github.com/jadalaramani/Ansible_k8s_gha/blob/main/Ansible/k8s_deployment.yaml
```
---

## 🛠 Troubleshooting

* **`No inventory was parsed` warning:**
  Safe to ignore since we’re running on localhost with `connection: local`.

* **`localhost:8080` connection refused:**
  Kubeconfig or AWS credentials not set. Check that the `KUBECONFIG_DATA` secret is correct and AWS creds are configured.

* **Push rejected on GitHub:**
  Add `fetch-depth: 0` to checkout step and do a `git pull --rebase` before pushing.

* **Manifest path not found:**
  Use relative path `{{ playbook_dir }}/k8s_deployment.yaml` instead of hardcoding `/home/ec2-user`.

---

## ✅ Success Criteria

* Docker image built, scanned, and pushed to Docker Hub.
* Kubernetes manifest updated with the new image tag.
* Ansible applies the manifest to EKS.
* GitHub Actions job completes successfully.

---

---

This single document can be copied directly into your repository as `README.md` and serves as full project documentation.

Do you want me to also include a **sample secrets setup screenshot/table** (how to base64 encode kubeconfig) in this README?

