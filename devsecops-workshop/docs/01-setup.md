# 1. Install everything from scratch

This guide is for **Windows 10 or 11**. At the end you will have a Linux terminal on your laptop with Git, Docker, the GitHub CLI, kubectl and minikube.

You need about 8 GB of RAM, 20 GB of free disk space and a free [GitHub account](https://github.com/signup).

Run the commands one line at a time. Lines starting with `#` are explanations.

## Step 1: Install WSL 2 (Linux inside Windows)

Open **PowerShell as Administrator** (Start menu, type PowerShell, right-click, Run as administrator).

```powershell
# Install WSL 2 with Ubuntu
wsl --install
```

Restart the computer. After the restart an Ubuntu window opens and asks you to create a Linux username and password. Remember the password; you need it for `sudo`.

Check it, in PowerShell:

```powershell
# Ubuntu should be listed with VERSION 2
wsl -l -v
```

If VERSION shows 1, run `wsl --set-default-version 2` and then `wsl --set-version Ubuntu 2`.

## Step 2: Install Docker Desktop

In PowerShell:

```powershell
winget install -e --id Docker.DockerDesktop
```

Then:

1. Start **Docker Desktop** from the Start menu and accept the terms.
2. Open **Settings**, then **General**, and make sure **Use the WSL 2 based engine** is ticked.
3. Open **Settings**, then **Resources**, then **WSL integration**, and switch on **Ubuntu**.
4. Click **Apply & restart**.

Docker Desktop must be running whenever you use Docker or minikube.

## Step 3: Install VS Code

In PowerShell:

```powershell
winget install -e --id Microsoft.VisualStudioCode
```

Open VS Code once, go to Extensions (Ctrl+Shift+X) and install the extension named **WSL**.

**From here on, every command is typed in the Ubuntu terminal**, not PowerShell. Open it from the Start menu by typing Ubuntu.

## Step 4: Update Ubuntu and install Git

```bash
# Refresh the package list and upgrade
sudo apt update && sudo apt upgrade -y

# Install Git and a few helpers
sudo apt install -y git curl tar

# Tell Git who you are (use the email of your GitHub account)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Step 5: Check Docker works inside Ubuntu

```bash
docker run hello-world
```

You should see "Hello from Docker!". If you see "command not found" or "cannot connect", go back to Step 2 and check the WSL integration switch, then close and reopen Ubuntu.

## Step 6: Install the GitHub CLI (gh)

```bash
sudo apt install -y gh
gh --version
```

Sign in:

```bash
gh auth login
```

Answer the questions like this:

- Where do you use GitHub? **GitHub.com**
- Preferred protocol? **HTTPS**
- Authenticate Git with your GitHub credentials? **Yes**
- How would you like to authenticate? **Login with a web browser**

It prints a one-time code such as `ABCD-1234`. The terminal may show errors about not being able to open a browser; that is normal in WSL. Do **not** press Ctrl+C. Open <https://github.com/login/device> in your Windows browser, type the code and click **Authorize**. The terminal then says "Logged in as ...".

```bash
# Make git push use this login, so it never asks for a password
gh auth setup-git

# Confirm
gh auth status
```

## Step 7: Install kubectl (the remote control for Kubernetes)

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
kubectl version --client
```

## Step 8: Install minikube (Kubernetes on one laptop)

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
rm minikube-linux-amd64
minikube version
```

## Step 9: Start your cluster

```bash
# The first start downloads about 1 GB
minikube start --driver=docker

# One node should show STATUS Ready
kubectl get nodes
```

## Step 10: Final check

Run all of these. Each one should print a version or a healthy status, with no error.

```bash
git --version
docker --version
gh auth status
kubectl get nodes
minikube status
code --version
```

When they all work, go to the [lab](02-lab.md).

## Everyday commands

```bash
minikube start      # start the cluster
minikube status     # is it running?
minikube stop       # stop it, keep everything
minikube delete     # remove it completely
```

## Not on Windows?

- **Ubuntu or another Linux:** skip Steps 1 to 3. Install Docker with `sudo apt install -y docker.io`, run `sudo usermod -aG docker $USER`, log out and in again, then continue from Step 4.
- **macOS:** install [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/), then use Homebrew: `brew install git gh kubectl minikube`.
