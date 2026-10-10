# From Code to Confidence: DevSecOps Workshop

Hands-on material from the DevSecOps workshop at BCAS Campus (10 October 2026).

You will take a tiny web page from an empty folder to a running app on Kubernetes, with a pipeline that checks every change for leaked secrets and deploys it automatically.

**Covers:** Linux, Git and GitHub, Docker, Kubernetes (minikube) and CI/CD with GitHub Actions.

## What you will build

```
You push code  ->  CI checks it  ->  Docker packs it  ->  CD deploys it  ->  The page is live
  (git push)      (any secrets?)    (container image)     (Kubernetes)       (your browser)
```

## Follow these in order

| Step | Guide | Time |
|---|---|---|
| 1 | [Install everything from scratch](docs/01-setup.md) | 45 to 60 min |
| 2 | [Do the lab](docs/02-lab.md) | 60 min |
| 3 | [Understand the words](docs/03-concepts.md) | 10 min read |

## What is in this repository

```
docs/                       the four guides above
lab/                        the finished project files you copy in the lab
  index.html                the web page
  Dockerfile                how to pack the page into a container
  k8s/app.yaml              how Kubernetes should run it (2 copies + 1 address)
  .github/workflows/
    ci-cd.yml               the pipeline: check, build, deploy
```

The pipeline file sits inside `lab/` on purpose, so it does not run in this repository. It starts working when you copy it into your own repository during the lab.

## The one idea to remember

The commands are not the important part. The flow is:

**Plan -> Code -> Check -> Build -> Test -> Deploy -> Monitor**

Security is a small check inside every step, not something done at the end.

## Safety rules

- Keep your lab repository **private**. The lab connects your laptop to GitHub as a runner, and a public repository would let strangers send jobs to it.
- Never commit a real password, API key or token. If you do, change that secret immediately; deleting the file is not enough, because Git keeps the history.
- Use only made-up events and contact details.

## Facilitator

Madialagan Ajith Kumara, DevOps and Platform Engineer at N-able. CKA and AWS Certified Solutions Architect.

[LinkedIn](https://www.linkedin.com/in/ajithkumara-madialagan/)

[YouTube] (https://www.youtube.com/@AkOpsLabs)
