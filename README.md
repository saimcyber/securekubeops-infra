# securekubeops-infra

Kubernetes deployment manifests for **[SecureKubeOps](https://github.com/saimcyber/SecureKubeOps)** —
the infrastructure half of a GitOps-style DevSecOps pipeline.

The application code and CI/CD live in
**[SecureKubeOps](https://github.com/saimcyber/SecureKubeOps)**. This repository holds
only the desired cluster state, so the pipeline can update the running image by
committing here rather than by pushing to the cluster directly.

## Layout

```
dev/       Deployment + Service for the dev environment
staging/   Deployment + Service for the staging environment
prod/      Deployment + Service for the production environment
```

Each environment defines:

- a **Deployment** running the `saimcyber/securekubeops` image, pinned to an exact
  commit SHA (never `latest`), with liveness/readiness probes on `/health` and
  CPU/memory requests and limits set
- a hardened **securityContext** — non-root user, `allowPrivilegeEscalation: false`
- a **Service** exposing the app inside the cluster

## How the pipeline uses this repo

1. `SecureKubeOps` builds and scans a new image, then pushes it tagged with the
   Git SHA.
2. The pipeline clones this repo, rewrites the `image:` field in
   `dev/deployment.yaml`, and commits the change.
3. Applying the manifests (`kubectl apply -f dev/`) rolls the cluster to the new
   image.

## Apply manually

```bash
kubectl apply -f dev/       # or staging/ , prod/
```

## License

MIT — see [LICENSE](LICENSE).
