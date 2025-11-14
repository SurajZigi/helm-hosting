# Ainon Helm Chart Repository

Welcome to the Ainon Helm Chart repository! This repository hosts Helm charts for the Ainon application. Charts are packaged and published automatically via GitHub Actions and served through GitHub Pages. You can both use Helm to deploy charts and download packaged charts directly via the web page.

---

## Repository URL

This Helm chart repository is publicly accessible at: https://SurajZigi.github.io/helm-hosting/


---

## How to Use This Helm Chart Repository

### 1. Add the Helm Repository

Add the Ainon Helm repo to your local Helm client:

helm repo add ainon https://SurajZigi.github.io/helm-hosting/
helm repo update


### 2. Search for Available Charts

Search for charts available in this repo:

helm search repo ainon


### 3. Install a Helm Chart

Install the Ainon Helm chart with default values:

helm install my-release ainon/ainon-helm


Replace `my-release` with your desired release name.

---

## Overriding Default Values

Customize charts by overriding values as needed.

### Using `--set`

Override inline values:

helm install my-release ainon/ainon-helm --set key1=value1,key2=value2


Example:

helm install my-release ainon/ainon-helm --set replicaCount=3,service.type=LoadBalancer


### Using a Custom `values.yaml`

Create a YAML file with your custom configurations:

replicaCount: 3
service:
type: LoadBalancer
image:
tag: "v2.0.1"


Install using the custom values file:

helm install my-release ainon/ainon-helm -f custom-values.yaml


---

## Download Helm Charts via Webpage

You can download packaged Helm charts directly from the browser:

- Visit:  
  [https://SurajZigi.github.io/helm-hosting/](https://SurajZigi.github.io/helm-hosting/)

- Click on any `.tgz` file to download it.

- To install a downloaded chart locally:

helm install my-release ./path-to-chart-archive.tgz



---

## About the GitHub Actions Pipeline

The Helm charts and repository index (`index.yaml`) are automatically packaged and published via this GitHub Actions workflow on every push to the `main` branch of the source repo (`ainon-helm-repo`):

name: Release Helm Chart Webpage

on:
push:
branches:
- main

jobs:
release:
runs-on: ubuntu-latest
env:
GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

steps:
  - uses: actions/checkout@v3

  - uses: azure/setup-helm@v3

  - run: helm package ./ainon-helm

  - env:
      REPO_TOKEN: ${{ secrets.REPO_TOKEN }}
    run: |
      git clone https://x-access-token:${REPO_TOKEN}@github.com/SurajZigi/helm-hosting.git temp-helm-repo
      cp ./ainon-helm-*.tgz temp-helm-repo/
      cd temp-helm-repo
      helm repo index . --url https://SurajZigi.github.io/helm-hosting/
      echo "<html><body><h1>Helm Chart Packages</h1><ul>" > index.html
      for f in *.tgz; do
        echo "<li><a href=\"$f\">$f</a></li>"
      done >> index.html
      echo "</ul></body></html>" >> index.html
      git config user.name "github-actions[bot]"
      git config user.email "github-actions[bot]@users.noreply.github.com"
      git add index.yaml index.html *.tgz
      git commit -m "Release new chart version from $GITHUB_SHA" || echo "No changes to commit"
      git push origin main



### Important:
- `REPO_TOKEN` must be a Personal Access Token (classic or fine-grained) with write access to `helm-hosting`.
- GitHub Pages is enabled on `helm-hosting` repo from the `main` branch root, serving the charts and webpage.
- Published charts and `index.yaml` are accessible via the repository URL.

---

## Troubleshooting Tips

- Run `helm repo update` after adding or updating this repo.
- Check connectivity to `https://SurajZigi.github.io/helm-hosting/index.yaml`.
- Review `values.yaml` in the source repo for configurable parameters.
- If you see permission errors, verify token scopes and secrets configuration.
- For download issues, check GitHub Pages settings and allow some time for publishing latency.
