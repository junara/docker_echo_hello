# Publish docker image with GitHub Packages Container registry

Publish docker image with GitHub Packages Container registry.

Deployment: Only tagging on GitHub. At tagging, GitHub Actions is triggered, image is build, latest and tag name is
tagged and push image to GitHub Packages Container registry in your repository.

## About this docker image

This image is simple echo image. Dockerfile is following.

```dockerfile
FROM busybox
LABEL org.opencontainers.image.description="Echo message to all USAKAI."
CMD ["echo", "We love rabbits !"]
```

## Build

### Build image thorough GitHub Actions

When you tagged branch, GitHub Actions build and push to GitHub Actions and deploy to GitHub Packages Container
registry.

```yml
on:
  push:
    tags:
      - '*'
```

Images is tagged to `#{tag name}` and `latest`.

```yml
VERSION=$(echo ${{ github.ref }} | sed -e "s#refs/tags/##g")
```

See `.github/workflows/deploy.yml`.

## Pull

### Pull image from GitHub Packages Container registry

If you want to use public available image to GitHub Packages, you can pull image by following command.

```shell
docker image pull ghcr.io/junara/docker_echo_hello:latest
```

Ref. https://docs.github.com/ja/packages/working-with-a-github-packages-registry/working-with-the-container-registry#pull-by-name


```shell
docker_echo_hello % docker image ls                                          
REPOSITORY                         TAG       IMAGE ID       CREATED        SIZE
ghcr.io/junara/docker_echo_hello   latest    4cec2900b7a5   47 hours ago   1.24MB
```

## Test image

Test this image by following commands.

```shell
docker container create --name docker_echo_hello ghcr.io/junara/docker_echo_hello
docker container start -i docker_echo_hello
# We love rabbits !
```

## Delete container and image

Delete container.

```shell
docker container rm docker_echo_hello
# docker_echo_hello
```

Delete image.

```shell
docker image rm ghcr.io/junara/docker_echo_hello
```

## etc
### Build image locally

You can build the Dockerfile locally.

Pull this repository and build image in local by following command.

```shell
git clone git@github.com:junara/docker_echo_hello.git
docker image build . -t docker_echo_hello
```

Check image

```shell
docker image ls

# REPOSITORY                 TAG       IMAGE ID       CREATED       SIZE
# docker_echo_hello          latest    263ac394977b   5 days ago    1.24MB
```

Create container

```shell
docker container create --name docker_echo_hello docker_echo_hello
docker container start -i docker_echo_hello
# We love rabbits !
```

Delete container and image
```shell
docker container rm docker_echo_hello
docker image rm docker_echo_hello
```
### Using private package

If your GitHub Packages Container registry is private, you can pull image by following command via personal access
token.

* create personal access token

See https://docs.github.com/ja/packages/working-with-a-github-packages-registry/working-with-the-container-registry

Personal access token needs `read:packages` permission.

* login

```shell
export CR_PAT=#{YOUR_GITHUB_PAT}
echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
```

* pull image ( equal to public package.)

```shell
docker image pull ghcr.io/junara/docker_echo_hello:latest
```

* test image (see above)

* delete container and image (see above)

* loguout

```shell
docker logout ghcr.io
# Removing login credentials for ghcr.io
```
