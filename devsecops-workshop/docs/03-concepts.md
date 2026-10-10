# 3. Understand the words

## DevOps and DevSecOps

| Word | Real meaning | Simply |
|---|---|---|
| DevOps | A way of working where the people who build software and the people who run it work as one team, and automate the path from code to users | Builders and runners, one team |
| DevSecOps | DevOps with a security check built into every stage, instead of one check at the end | Safety at every step |
| CI (Continuous Integration) | Every change is automatically built and checked as soon as it is pushed | The robot checks it |
| CD (Continuous Deployment) | Every approved change is automatically delivered to where it runs | The robot ships it |
| Pipeline | The ordered list of automatic steps a change goes through | The robot's job list |

## Think of a restaurant

| In the restaurant | In software |
|---|---|
| The chef cooks the food | The developer writes the code |
| The waiter serves it | Operations delivers the app and keeps it running |
| The food inspector | Security checks it is safe |
| The customer | The user |

If the chef and the waiter never talk, the customer waits.

## Git and GitHub

| Word | Meaning |
|---|---|
| Git | A tool on your laptop that saves checkpoints of your work |
| GitHub | A website where a team shares those checkpoints |
| Repository | The project folder with its whole history |
| Branch | Your own safe copy to work on, without touching the main version |
| Commit | A saved step, with a message saying what changed |
| Push | Send your commits from your laptop up to GitHub |
| Pull request | "Please check my change and add it to main" |
| Merge | Add the approved change to main |

## Containers and Kubernetes

| Word | Real meaning | Simply |
|---|---|---|
| Container | A lightweight, isolated package holding an app and everything it needs to run | A sealed lunch packet |
| Image | The saved, read-only package a container is started from | The packet on the shelf |
| Docker | A tool that builds container images and runs containers from them | The packing machine |
| Dockerfile | A text file with the instructions for building an image | The packing instructions |
| Kubernetes (K8s) | An open-source system that deploys, scales and heals containers automatically, across many machines | The manager |
| Cluster | All the machines Kubernetes manages | The whole restaurant |
| Node | One machine in the cluster | One kitchen |
| Pod | The smallest unit Kubernetes runs: one or more containers sharing a network and storage | One running copy of the app |
| Deployment | A rule that says which app to run and how many copies, and keeps it true during crashes and updates | "Always keep 2 running" |
| Replica | One copy of a pod | One of the copies |
| Service | One stable network address that sends traffic to healthy pods, even when pods are replaced | The front counter |
| minikube | A one-machine Kubernetes cluster for learning | A practice kitchen |
| kubectl | The command-line tool for talking to Kubernetes | The remote control |

How they fit together: **Docker** builds a **container**. **Kubernetes** runs it inside a **pod**. A **Deployment** keeps the pods alive. A **Service** lets users reach them.

## The eight stages

| Stage | Question it answers | Example tools | Security check |
|---|---|---|---|
| 1 Plan | What are we changing, and what could go wrong? | Jira, GitHub Issues | Think about risks |
| 2 Code | Write the change and save every version | Git, GitHub | Secret scanning |
| 3 Build | Pack the app so it runs anywhere | Docker | Dependency scan |
| 4 Test | Does it work? Is it safe? | GitHub Actions, Jenkins | Code and web scans |
| 5 Release | A teammate reviews and approves | Pull requests | A second person reviews |
| 6 Deploy | Put it on the servers | Kubernetes, AWS | Safe configuration |
| 7 Operate | Keep it running | Linux, Terraform | Patching, access control |
| 8 Monitor | Watch, learn, go back to Plan | Grafana, Prometheus | Alerts, audit logs |

## Command cheat sheet

```bash
# Linux
pwd                     # where am I?
ls -a                   # what is in this folder, including hidden files?
mkdir name              # make a folder
cd name                 # go into a folder
cat file                # show a file

# Git
git status              # what has changed?
git checkout -b name    # make a branch
git add .               # choose what to save
git commit -m "text"    # save a checkpoint
git push                # send to GitHub

# GitHub CLI
gh repo create          # create a repository
gh pr create --fill     # open a pull request
gh pr checks --watch    # watch the checks
gh pr merge --merge     # merge it
gh run watch            # watch the pipeline

# Kubernetes
kubectl get nodes       # is the cluster awake?
kubectl get pods        # show running copies
kubectl delete pod NAME # remove one copy (a new one appears)
kubectl port-forward svc/campus-events 8080:80   # open the app locally
```

## A learning path

1. Linux and networking basics
2. Git and GitHub
3. One scripting language: Bash or Python
4. Docker, then Kubernetes
5. One cloud (AWS is a good start) and Terraform
6. CI/CD and monitoring, joined into one project you can show
