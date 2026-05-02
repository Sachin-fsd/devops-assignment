# 🚀 DevOps Screening Assignment Extravaganza

Hey there! Welcome to this epic DevOps screening assignment repo. We've got a containerized Flask app, a fancy Docker Compose setup, a GitHub Actions CI pipeline that pushes images to GHCR, and some Terraform magic for infrastructure. Let's dive in!

Name: Sachin
Roll No: 2301350008
Course: BTech CSE FSD
Semester: 6

## 🏠 Local Setup Shenanigans

### No Docker? No Problem!
1. **Get Your Python On**: Make sure Python 3.11 is chilling on your machine.
2. **Virtual Environment Vibes**:
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # Or on Windows: venv\Scripts\activate
   pip install -r app/requirements.txt
   ```
3. **Launch the App**:
   ```bash
   python app/app.py
   ```
   Boom! Your app is live at [http://localhost:5000](http://localhost:5000).

### Docker Compose Party Mode
1. **Fire It Up**:
   ```bash
   docker-compose up --build
   ```
   This builds your Flask app and grabs Redis like a boss.
2. **Hit the App**: Check it out at [http://localhost:8080](http://localhost:8080).
   *Pro tip: We're mapping container port 5000 to host port 8080 for that sweet access.*

---

## 🔄 CI Pipeline Magic
The pipeline lives in `.github/workflows/ci.yml` and kicks off on every `push` to `main` and all `pull_requests`.

### The Flow & Why We Do It:
- **Grab the Code**: `actions/checkout@v4` pulls in the repo goodies.
- **Python Setup**: Locks in Python 3.11 to match prod/container vibes.
- **Install the Goods**: Slaps on the exact deps from `requirements.txt`.
- **Test Time**: Runs Pytest to catch bugs before they ruin the party. Fails? Pipeline stops – no broken images here!
- **GHCR Login**: (Bonus points!) Uses `GITHUB_TOKEN` for that registry auth.
- **Build & Ship**: (Bonus alert!) Crafts the image and pushes to GHCR (main branch pushes only).

---

## 💡 Design Decisions That Slay
- **Base Image Pick (`python:3.11-slim`)**: Went slim for tiny footprint – saves space, speeds up pulls, keeps security tight.
- **Non-Root User Power**: Created `appuser` in the `Dockerfile`. Non-root is the way to go, blocking those pesky container escape attempts.
- **Layer Caching Wizardry**: Copy and install `requirements.txt` first. Docker caches layers, so deps only reinstall if the file changes – efficiency FTW!
- **Healthcheck Harmony in Compose**: App service waits for `redis` to be `service_healthy`. No starting the app until Redis is ready to roll.

---

## 🌟 Future Glow-Ups
- **Multi-Stage Builds**: For ultra-slim prod images, I'd multi-stage it – build deps in one stage, ship only the essentials.
- **Secret Sauce Management**: GitHub Actions handles `GITHUB_TOKEN`, but for Terraform/cloud, I'd hook up AWS Secrets Manager or Vault.
- **State Management**: Terraform needs a remote backend (S3 + DynamoDB) for team state consistency.
- **Test Expansion**: Add integration tests to verify Flask-Redis connection in containers.
