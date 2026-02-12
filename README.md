# Node.js Demo App - CI/CD Pipeline

**DevOps Internship - Task 1**

## 🚀 Quick Setup (15 minutes)

### 1. Create DockerHub Account
- Go to https://hub.docker.com/signup
- Create account and verify email
- Go to Account Settings → Security → New Access Token
- Name it "GitHub Actions" and copy the token

### 2. Create GitHub Repository
- Go to https://github.com/new
- Name: `nodejs-demo-app`
- Make it Public
- Don't initialize with README
- Create repository

### 3. Add GitHub Secrets
In your repo: Settings → Secrets and variables → Actions → New repository secret

Add these TWO secrets:
- `DOCKER_USERNAME` = your DockerHub username
- `DOCKER_PASSWORD` = your DockerHub access token

### 4. Upload Code
Download all files from this folder and upload to GitHub:
- Go to your repo
- Click "uploading an existing file"
- Drag ALL files (including .github folder!)
- Commit changes

### 5. Watch it Run!
- Go to Actions tab in your repo
- Your pipeline will run automatically
- All 3 jobs should complete with ✅

## 📁 Project Structure

```
nodejs-demo-app/
├── .github/
│   └── workflows/
│       └── main.yml       # CI/CD pipeline
├── index.js               # Express app
├── index.test.js          # Tests
├── package.json           # Dependencies
├── Dockerfile             # Container config
├── .gitignore
├── .dockerignore
└── README.md
```

## 🔄 CI/CD Pipeline Flow

```
Push to main → Run Tests → Build Docker Image → Push to DockerHub → Deploy
```

**3 Jobs:**
1. **Test** - Runs automated tests
2. **Build-and-Push** - Builds Docker image and pushes to DockerHub
3. **Deploy** - Confirms deployment success

## 📝 Interview Questions & Answers

### 1. What is CI/CD?

**CI/CD = Continuous Integration + Continuous Deployment**

- **CI (Continuous Integration)**: Automatically merge code changes and run tests on every commit
- **CD (Continuous Deployment)**: Automatically deploy passing code to production

**Benefits**: Faster releases, fewer bugs, automated testing, consistent deployments

---

### 2. How do GitHub Actions work?

GitHub Actions automates workflows triggered by events (push, PR, schedule).

**Key Parts:**
- **Workflows**: YAML files in `.github/workflows/`
- **Events**: Triggers (push, pull_request, etc.)
- **Jobs**: Tasks that run on virtual machines
- **Steps**: Individual commands or actions
- **Actions**: Reusable code blocks (checkout, setup-node)

**Example:**
```yaml
on: push           # Event
jobs:
  build:           # Job
    runs-on: ubuntu-latest
    steps:         # Steps
      - uses: actions/checkout@v3
      - run: npm test
```

---

### 3. What are runners?

**Runners** = Servers that execute your workflows

**Types:**

**GitHub-hosted runners:**
- Provided by GitHub (ubuntu-latest, windows, macos)
- Fresh VM for each job
- Free for public repos
- Pre-installed with common tools

**Self-hosted runners:**
- Your own servers
- Full control
- No minute limits
- Can access private networks

---

### 4. Difference between jobs and steps

| Jobs | Steps |
|------|-------|
| Run in parallel (default) | Run sequentially |
| Separate virtual machines | Same machine |
| Can depend on other jobs | Part of a single job |
| Independent environments | Share job environment |

**Example:**
```yaml
jobs:
  test:          # Job 1
    steps:
      - checkout  # Step 1
      - test      # Step 2
  
  build:         # Job 2 (parallel)
    needs: test  # Dependency
    steps:
      - build    # Step 1
```

---

### 5. How to secure secrets in GitHub Actions?

**Best Practices:**

1. **Use GitHub Secrets** (Settings → Secrets)
   ```yaml
   password: ${{ secrets.DB_PASSWORD }}
   ```

2. **Never hardcode credentials**
   - ❌ Bad: `password: mypass123`
   - ✅ Good: `password: ${{ secrets.PASSWORD }}`

3. **Secrets are:**
   - Encrypted at rest
   - Masked in logs (shows ***)
   - Not exposed in forks

4. **Use environment secrets** for production
5. **Rotate regularly**
6. **Use OIDC** for cloud providers (AWS, Azure, GCP)

---

### 6. How to handle deployment errors?

**Strategies:**

1. **Fail fast:**
```yaml
- name: Deploy
  run: ./deploy.sh
  continue-on-error: false  # Stop on error
```

2. **Conditional rollback:**
```yaml
- name: Rollback
  if: failure()
  run: ./rollback.sh
```

3. **Health checks:**
```yaml
- name: Verify
  run: curl -f http://app.com/health || exit 1
```

4. **Notifications:**
```yaml
- name: Notify on failure
  if: failure()
  uses: slack-action
```

5. **Manual approval** for production
6. **Monitor and alert** with logging tools

---

### 7. Explain the Docker build-push workflow

**Process:**

1. **Setup Docker Buildx** (enhanced builder)
2. **Login to registry:**
   ```yaml
   - uses: docker/login-action@v2
     with:
       username: ${{ secrets.DOCKER_USERNAME }}
       password: ${{ secrets.DOCKER_PASSWORD }}
   ```

3. **Build image:**
   - Reads Dockerfile
   - Executes instructions
   - Creates layers (cached)

4. **Push to DockerHub:**
   - Uploads only new layers
   - Tags image (latest, version)

**Complete action:**
```yaml
- uses: docker/build-push-action@v4
  with:
    context: .
    push: true
    tags: user/app:latest
```

**Benefits:**
- Consistent environments
- Easy rollbacks
- Fast deployments

---

### 8. How can you test a CI/CD pipeline locally?

**Methods:**

1. **Act (GitHub Actions locally):**
```bash
# Install
brew install act

# Run workflow
act push
act -j build  # Specific job
```

2. **Docker testing:**
```bash
docker build -t test-app .
docker run -p 3000:3000 test-app
```

3. **Run commands directly:**
```bash
npm install
npm test
npm run build
```

4. **Validate YAML:**
```bash
yamllint .github/workflows/main.yml
```

5. **Use draft PRs** or test branches

**Limitations:**
- Some actions need GitHub environment
- Secrets not available locally
- Runner differences

---

## 🐳 Docker Commands

```bash
# Build locally
docker build -t nodejs-demo-app .

# Run locally
docker run -p 3000:3000 nodejs-demo-app

# Test
curl http://localhost:3000
curl http://localhost:3000/health

# Pull from DockerHub
docker pull YOUR_USERNAME/nodejs-demo-app:latest
```

## 📸 Screenshots Needed

Take screenshots of:
1. GitHub Actions - all jobs passed ✅
2. DockerHub - your image uploaded
3. GitHub repo with all files
4. Secrets configured

## ✅ Submission Checklist

- [ ] GitHub repo created
- [ ] All files uploaded (including .github folder!)
- [ ] DockerHub secrets added
- [ ] Pipeline ran successfully
- [ ] Screenshots taken
- [ ] Repo link ready to submit

## 🔗 Resources

- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Docker Docs](https://docs.docker.com/)
- [Express.js](https://expressjs.com/)

---

**Created for DevOps Internship - Task 1**
