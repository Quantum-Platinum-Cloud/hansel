# Hansel

Hansel generates empty linux packages. These packages can be installed to track dependencies manually added to a container image.

<!-- TODO: make a gif? -->

## Usage

You can add hansel to an image, and generate + install tracking packages for dependencies in a single step:
```dockerfile
FROM node:18-alpine

COPY --from=ghcr.io/Shopify/hansel:latest /usr/bin/hansel /usr/bin/hansel
RUN hansel --name node --version "$(node -v)" --install
```

You can use hansel in a multistep build to generate and install separately:
```dockerfile
FROM ghcr.io/Shopify/hansel:latest AS crumbs
RUN hansel --name rando-thing --version v1.2.3 --debian

FROM debian:bullseye
RUN curl -o /usr/bin/rando-thing https://rando.thing/v1.2.3/unsigned-blob-yolo
COPY --from=crumbs /rando-thing*.deb /tmp/rando-thing.deb
RUN dpkg -i /tmp/rando-thing.deb && \
    rm /tmp/rando-thing.deb
```

The name is inspired by [Hansel and Gretel](https://en.wikipedia.org/wiki/Hansel_and_Gretel), as the packages are breadcrumbs left for container scanners to identify.
