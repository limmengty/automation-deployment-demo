# automation-deployment-demo

💡 **Purpose**

This repository contains an opinionated Jenkins pipeline for building, pushing, and deploying a Dockerized Node-like application (example: `employee-api`). The pipeline is designed with security and DevOps best practices: parameterization, credential bindings, workspace hygiene, and optional lint/test steps.

---

## 🔧 Jenkins pipeline overview

- Pipeline file: `docker.jenkinsfile`
- Main features:
  - Parameterized `GIT_BRANCH`, `DOCKER_REPO`, `DOCKER_USER`, `GIT_REPO`, `DEPLOY_HOST`, `DEPLOY_DIR`, `SKIP_TESTS`
  - Short Git commit + build number Docker tags: `<short-sha>-<build-number>`
  - Lint & test stage (runs when `package.json` exists; can be skipped)
  - Docker push using Jenkins credentials
  - Deploy via SSH (password-based) to a specific directory and starts container
  - Telegram notifications for success/failure (via Jenkins credentials)
  - `cleanWs()` used to maintain a clean workspace

---

## 🔐 Jenkins credentials required

Create the following credentials in Jenkins (use secure IDs as shown):

- `docker_hub_password` — *Secret text* (Docker Hub password)
- `deploy_ssh_pwd` — *Username with password* (SSH user and password for deploy host)
- `telegram_token` — *Secret text* (Telegram bot token)
- `telegram_chat_id` — *Secret text* (Telegram chat ID)

Notes:
- The pipeline expects the deploy username to be stored in `deploy_ssh_pwd` username field; it will use `sshpass` to authenticate via password.
- Ensure the Jenkins agent has `sshpass` available (the pipeline attempts apt install, but this may require sudo). Prefer installing `sshpass` on agent images ahead of time.

---

## ▶️ How to run

1. Create the credentials listed above in Jenkins.
2. Create or configure a Jenkins pipeline job using the `docker.jenkinsfile` in this repo.
3. Run the job and set parameters as needed (for example `DEPLOY_DIR=/otp/foldername`).

---

## ✅ Recommendations & next steps

- Consider migrating to SSH key-based auth (`sshUserPrivateKey`) for deployments (more secure, avoids `sshpass`).
- Add image scanning (Trivy) before pushing images.
- Add a smoke test after deploy to validate the service.
- Use a non-root service user on target hosts and expose only required ports.

---

If you want, I can also: add image scanning to the pipeline, add automated smoke tests, or switch the deploy step to SSH key-based auth. Just tell me which one to prioritize.