# github-actions-setup-container-build-env

Composite action to set up building and publishing a container-based app.

It authenticates to Google Cloud, configures Docker for Artifact Registry, builds and
pushes the application image, and then triggers the deployment pipeline via a
`repository_dispatch` event.

## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: bootiful-media-mogul/github-actions-setup-container-build-env@v1
        id: setup
        with:
          app_name: ${{ github.event.repository.name }}
          gcloud_project: ${{ secrets.GCLOUD_PROJECT }}
          gcloud_sa_key: ${{ secrets.GCLOUD_SA_KEY }}
          gh_token: ${{ secrets.GH_TOKEN }}

      - run: echo "pushed ${{ steps.setup.outputs.image }}"
```

## Inputs

| Name | Required | Default | Description |
| --- | --- | --- | --- |
| `app_name` | yes | — | The name of the application to be deployed. |
| `gcloud_project` | yes | — | The Google Cloud project name. |
| `gcloud_sa_key` | yes | — | The service account key JSON for Google Cloud. |
| `gh_token` | yes | — | GitHub access token / PAT used to dispatch the pipeline. |
| `dockerfile` | no | `.github/workflows/Dockerfile` | Path to the Dockerfile, relative to the workspace root. |
| `artifact_registry` | no | `mogul-artifact-registry` | The Artifact Registry repository to publish to. |
| `registry_host` | no | `us-docker.pkg.dev` | The Artifact Registry host. |

## Outputs

| Name | Description |
| --- | --- |
| `image` | The fully qualified image reference that was pushed. |

The image is published as `<registry_host>/<gcloud_project>/<artifact_registry>/<app_name>:latest`.
