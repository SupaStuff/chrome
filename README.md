# Chrome image

This image should allow you to use Puppeteer in a Docker container.
The base image is pretty much inspired entirely by the
[Runing Puppeteer in Docker](https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-puppeteer-in-docker)
section in their docs.

## Images

There are 3 images produced by this Dockerfile:

- `supastuff/chrome`: The base image with `google-chrome-stable` installed so that you can use headless chrome with your **locally** installed Puppeteer (`npm install --save puppeteer`).
- `supastuff/chrome:jupyter`: Python, Jupyter, and tslab installed on top of `supastuff/chrome`.
- `supastuff/chrome:vscode`: Image meant to be used with VSCode's devcontainers feature. Based on `supastuff/chrome:jupyter`. Adds user **vscode** with id 1000 and creates directories owned by **vscode** to prevent permission denied isssues.

## Usage

Use like any other Docker image. The more interesting image is `supastuff/chrome:vscode`.
Typically, I'll point _.devcontainer/.devcontainer.json_ to a _docker-compose.yaml_ that looks like this:

```yaml
version: "3"
services:
  vscode:
    environment:
      CONTAINER_USER: vscode
    user: ${CONTAINER_USER}
    image: supastuff/chrome:vscode

    volumes:
      - ..:/home/${CONTAINER_USER}/workspace:cached
      - node_modules:/home/${CONTAINER_USER}/workspace/node_modules
      - python:/home/${CONTAINER_USER}/.local
      - vscode_server:/home/${CONTAINER_USER}/.vscode-server/extensions
      - vscode_server_insiders:/home/${CONTAINER_USER}/.vscode-server-insiders/extensions
      - ~/.my_bash_history:/home/${CONTAINER_USER}/.my_bash_history:cached

    command: sleep infinity

    networks:
      - default

volumes:
  node_modules:
  python:
  vscode_server:
  vscode_server_insiders:
```

`tslab` is installed globally so that it can be used by VSCode's native .ipynb file support.
Select tslab as your kernel and it just works. But only because it's installed globally.
