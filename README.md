# Benchmarks unit converter

A small, self-contained Go web app for converting common metric and imperial length measurements. Enter a value in any row and the remaining rows update automatically. Fractional inches can be rounded to standard shop increments from 1/2 through 1/64. The displayed units can be customized per browser; millimeters, fractional inches, and decimal inches are shown by default.

## Run locally

```sh
go run .
```

Open <http://localhost:8080>. Set `PORT` to listen on another port.

## Test

```sh
go test ./...
```

## Build the container

```sh
docker build -t ghcr.io/YOUR_GITHUB_USER/mm-inches:latest .
docker push ghcr.io/YOUR_GITHUB_USER/mm-inches:latest
```

The final image is distroless, runs as a non-root user, and writes no application data to disk.

## Deploy to Kubernetes

1. Replace `ghcr.io/REPLACE_ME/mm-inches:latest` in `deploy/deployment.yaml` with the image you pushed. Prefer an immutable version tag or digest for GitOps.
2. Validate the rendered resources:

   ```sh
   kubectl kustomize deploy
   kubectl apply --dry-run=server -k deploy
   ```

3. Add `deploy/` to the appropriate Argo CD application, or apply it manually:

   ```sh
   kubectl apply -k deploy
   kubectl -n unit-conversions rollout status deployment/mm-inches
   ```

The included Service is cluster-internal. Expose it with the Gateway or Ingress pattern already used by your cluster rather than assuming a specific controller or certificate setup.

## Endpoints

- `/` — converter interface
- `/api/convert` — conversion endpoint used by the interface
- `/healthz` — liveness and readiness check
