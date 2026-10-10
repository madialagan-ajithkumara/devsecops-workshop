# 2. The lab: from an empty folder to a live app

Do the [setup](01-setup.md) first. Everything here is typed in the **Ubuntu terminal**, and Docker Desktop must be running.

You will use three terminal tabs:

- **Tab 1:** your commands
- **Tab 2:** the runner (it lets GitHub run the deploy on your laptop)
- **Tab 3:** the tunnel to the app

## Part 0: Get ready

```bash
# Start the cluster and check it
minikube start
kubectl get nodes
gh auth status

# Download this workshop repository (you will copy files from it)
git clone https://github.com/madialagan-ajithkumara/devsecops-workshop.git ~/devsecops-workshop

# Download the GitHub Actions runner program (only needed once)
cd ~
gh release download -R actions/runner -p 'actions-runner-linux-x64-*.tar.gz' -O runner.tar.gz
```

## Part 1: Create the project and put it on GitHub

```bash
# Make a project folder and go inside
mkdir ~/campus-demo
cd ~/campus-demo

# Start Git here, with main as the first branch
git init -b main

# Create a PRIVATE repository on GitHub and link this folder to it
gh repo create campus-demo --private --source=. --remote=origin

# Add the web page
cp ~/devsecops-workshop/lab/index.html .

# Look at it in VS Code
code .
```

Save your first checkpoint and send it to GitHub:

```bash
git status
git add .
git commit -m "Add events page"
git push -u origin main
```

Run `gh repo view --web` to see your repository in the browser.

## Part 2: Add the three instruction files

```bash
# How to pack the page into a container
cp ~/devsecops-workshop/lab/Dockerfile .

# How Kubernetes should run it
cp -r ~/devsecops-workshop/lab/k8s .

# The pipeline (the folder name starts with a dot, so it is hidden)
cp -r ~/devsecops-workshop/lab/.github .

# Check: you should see .github  Dockerfile  index.html  k8s
ls -a
```

Open each file in VS Code and read it:

| File | What it says |
|---|---|
| `Dockerfile` | Start from a ready web server (nginx) and copy our page inside |
| `k8s/app.yaml` | A Deployment that keeps 2 copies running, and a Service that gives them one address |
| `.github/workflows/ci-cd.yml` | Job `ci`: look for secrets, build the image. Job `cd`: build inside minikube and deploy |

## Part 3: Connect your laptop as a runner

Open **Tab 2**.

```bash
# Unpack the runner into its own folder
mkdir ~/runner
cd ~/runner
tar xzf ~/runner.tar.gz

# Find your GitHub username
GH_USER=$(gh api user -q .login)

# Ask GitHub for a one-time registration token
TOKEN=$(gh api -X POST repos/$GH_USER/campus-demo/actions/runners/registration-token -q .token)

# Register this laptop for your repository
./config.sh --url https://github.com/$GH_USER/campus-demo --token $TOKEN --unattended --name laptop

# Start listening. Leave this tab open.
./run.sh
```

It should print **Listening for Jobs**.

## Part 4: Push once and watch the pipeline

Back in **Tab 1**:

```bash
cd ~/campus-demo
git add .
git commit -m "Add Dockerfile, Kubernetes and CI/CD"
git push

# Watch ci run on GitHub, then cd run on your laptop
gh run watch

# Two copies of the app should be Running
kubectl get pods
```

Open **Tab 3**:

```bash
kubectl port-forward svc/campus-events 8080:80
```

Open <http://localhost:8080> in your browser. The page is live. You typed `git push`; you never typed "docker build" or "deploy".

## Part 5: Watch Kubernetes heal itself

In **Tab 1**:

```bash
kubectl get pods

# Copy one pod name from the list and delete it on purpose
kubectl delete pod PASTE-POD-NAME-HERE

# Still two pods: one has a new name and an age of a few seconds
kubectl get pods
```

## Part 6: Ship a real change (the main flow)

**Change -> Branch -> Commit -> Pull request -> Check -> Merge -> Deploy**

```bash
# A safe branch
git checkout -b add-tech-talk
```

In VS Code, add these two lines below the comment line in `index.html`, then save:

```html
  <h2>DevOps Tech Talk</h2>
  <p>28 October, Lecture Hall 2. Contact: techclub@example.edu</p>
```

```bash
git add .
git commit -m "Add DevOps tech talk event"
git push -u origin add-tech-talk

# Open a pull request
gh pr create --fill

# Watch the robot check it
gh pr checks --watch

# Merge it. This triggers the deploy.
gh pr merge --merge --delete-branch

# Watch ci, then cd
gh run watch
```

The tunnel in Tab 3 stops when the old pods are replaced. Press Ctrl+C there and run `kubectl port-forward svc/campus-events 8080:80` again, then refresh the browser. The new event is there, and nobody deployed it by hand.

## Part 7: Let the robot say no (the security check)

```bash
git checkout main
git pull
git checkout -b bad-change
```

In VS Code, add this line below the comment line, then save:

```html
  <!-- admin password = admin123 -->
```

```bash
git add .
git commit -m "update"
git push -u origin bad-change
gh pr create --fill
gh pr checks --watch
```

The check fails with a red X and the message "Possible secret found." The change is never built and never deployed. That is DevSecOps: the security check comes before production.

Clean up the bad change:

```bash
gh pr close bad-change --delete-branch
git checkout main
```

## Part 8: Stop and clean up

```bash
# Tab 2: press Ctrl+C to stop the runner
# Tab 3: press Ctrl+C to stop the tunnel

# Remove the app from Kubernetes
kubectl delete deployment campus-events
kubectl delete service campus-events

# Unregister the runner and delete its folder
cd ~/runner
GH_USER=$(gh api user -q .login)
TOKEN=$(gh api -X POST repos/$GH_USER/campus-demo/actions/runners/remove-token -q .token)
./config.sh remove --token $TOKEN
cd ~ && rm -rf ~/runner

# Stop the cluster
minikube stop
```

Keep your `campus-demo` repository: it is the first item in your GitHub portfolio.

## What to try next

1. Change `replicas: 2` to `replicas: 4` in `k8s/app.yaml`, push it through a pull request, and watch four pods appear.
2. Add a second check to the pipeline, for example one that fails if `index.html` has no `<title>`.
3. Write a short README for your `campus-demo` repository that explains the pipeline in your own words.
