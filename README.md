# hostinger-deploy

A reusable GitHub Action to deploy files to a Hostinger server via SSH and rsync, with optional post-deploy command execution.

## Usage

```yaml
- uses: pedromneto97/hostinger-deploy@v1.0.0
  with:
    ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
    ssh-host: ${{ secrets.SSH_HOST }}
    ssh-user: ${{ secrets.SSH_USER }}
    ssh-port: ${{ secrets.SSH_PORT }}
    local-path: ./dist/
    remote-path: /home/user/public_html/
    post-deploy-commands: sudo systemctl restart myapp # For VPS, optional commands to run after upload
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `ssh-private-key` | yes | — | SSH private key for authentication |
| `ssh-host` | yes | — | Remote server hostname or IP address |
| `ssh-user` | yes | — | SSH username |
| `ssh-port` | no | `22` | SSH port |
| `remote-path` | yes | — | Destination path on the remote server |
| `local-path` | yes | — | Local path of the artifact to upload |
| `rsync-options` | no | `-avz --delete` | rsync flags passed to the upload command |
| `post-deploy-commands` | no | `''` | Shell commands to run on the server via SSH after the upload |

## Secrets

Store sensitive values as [GitHub Actions secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions):

| Secret | Description |
|---|---|
| `SSH_PRIVATE_KEY` | Private key whose public counterpart is authorized on the server |
| `SSH_HOST` | Server hostname or IP |
| `SSH_USER` | SSH login username |
| `SSH_PORT` | SSH port (if non-standard) |

## Full workflow example

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7

      - name: Build
        run: npm ci && npm run build

      - uses: pedromneto97/hostinger-deploy@v1.0.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          ssh-host: ${{ secrets.SSH_HOST }}
          ssh-user: ${{ secrets.SSH_USER }}
          ssh-port: ${{ secrets.SSH_PORT }}
          local-path: ./dist/
          remote-path: /home/user/public_html/
          post-deploy-commands: |
            sudo chmod -R 755 /home/user/public_html
            sudo systemctl restart myapp
```
