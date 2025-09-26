<!-- Copilot instructions for contributors and automated coding agents -->
# Quick orientation for AI coding agents

This repository is a small Flask web app used as a Kubernetes tutorial/demo. The goal of edits should be to preserve the simple tutorial flow: build a Docker image, run locally, then deploy to Kubernetes (Minikube or a cloud provider).

Keep instructions concise and actionable. Focus on changes that keep the app runnable inside Docker and easy to deploy on k8s.

Key files to inspect when making changes
- `app.py` — simple Flask app (single route, renders `templates/index.html`). Modifying form fields should update the template and tests/manual checks.
- `dockerfile` — Docker build instructions. Changes here affect CI and local image builds. Keep the working directory `/app` and ensure `requirements.txt` is copied before `pip install` to leverage Docker layer caching.
- `requirements.txt` — pinned dependency `Flask==2.3.2`. Add minimal dependencies only and pin versions.
- `templates/index.html` and `static/style.css` — UI; keep relative paths `/static/style.css` stable.
- `K8S-Notes.md` and `README.md` — contain deployment and Minikube guidance. Respect the project’s documented workflows (image build, minikube load, kubectl apply).

What to change (and what to avoid)
- Safe edits: Small feature additions to `app.py`, adding endpoints, improving templates, small dependency bumps (test locally), adding Kubernetes manifests (`deployment.yaml`, `service.yaml`) alongside README instructions.
- Risky edits: Replacing the web framework, changing the exposed port (unless you update Dockerfile and README), removing the Dockerfile, or restructuring the repo layout — these break the tutorial flow.

Developer workflows and commands (explicit)
- Build Docker image locally (PowerShell on developer machines):
  docker build -t kubernetes-test-app:latest .
- Run image locally:
  docker run -p 5000:5000 kubernetes-test-app:latest
- With Minikube: build and load image, then deploy with a manifest (example in README):
  minikube start
  minikube image load kubernetes-test-app:latest
  kubectl apply -f deployment.yaml
  minikube service kubernetes-test-app

Patterns and conventions specific to this repo
- Minimal, single-file Flask app. Prefer small, focused edits. When adding routes, use the same pattern as `home()` (use `render_template`, avoid adding complex app factories unless necessary).
- Dockerfile naming: the repository uses `dockerfile` (lowercase, no extension). If you update tooling that expects `Dockerfile`, ensure either to rename the file or update CI manifests.
- Port convention: the app binds to 0.0.0.0:5000. If you change the port, update `dockerfile`, README, and any k8s manifests.

Integration points and external dependencies
- Docker images: CI/pipeline expects to build and push images to a registry (ECR/DockerHub). Keep image tags predictable (e.g., `<name>:latest`) or document changes in README.
- Kubernetes: cluster manifests are not present in-tree; adding `deployment.yaml` + `service.yaml` near the repo root is acceptable and should be referenced from README and `K8S-Notes.md`.

Examples from the codebase
- Form handling: `app.py` reads `request.form.get('name')` and renders `index.html` with a `message` variable — keep this pattern when adding fields.
- Docker layering: the `dockerfile` copies `requirements.txt` before running `pip install` to speed rebuilds; preserve that order.

Acceptance criteria for changes by automated agents
- The web app still runs with `python app.py` and inside the Docker image at port 5000.
- `docker build` completes without errors and `docker run -p 5000:5000` serves the index page and form flow.
- Any added Kubernetes manifests include `metadata.name: kubernetes-test-app` and use image `kubernetes-test-app:latest` by default unless documented otherwise.

If unsure, avoid large refactors and ask the maintainer. After making changes, update `README.md` or `K8S-Notes.md` with any new steps.

---
Please review and tell me if you'd like more detail in any area (CI examples, k8s manifest templates, or test instructions).
