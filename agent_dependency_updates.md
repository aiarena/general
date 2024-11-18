---
layout: default
title: Agent Dependency Updates
nav_order: 901
---

# Agent Dependency Updates

This is a list of steps for how to update the dependencies that agents use in the Arena Clients.

# Steps

## Updating the base image

These steps will update the [arenaclient-bot-base](https://hub.docker.com/r/aiarena/arenaclient-bot-base) image.

1. Before modifying the base image, bump the BOT_VERSION in the ci [here](https://github.com/aiarena/aiarena-docker-base/blob/master/.github/workflows/ci.yml#L13)
This will cause the new image to be build under the new version tag.
2. Modify the Docker.bot file in the [Docker.bot](https://github.com/aiarena/aiarena-docker-base/blob/master/docker/Dockerfile.bot) file with the relevant changes.
3. Push or PR the changes to master. Once the changes are on master the ci will build the new image and push to docker hub.

## Updating the SC2 AI match controller

These steps will update the [sc2-ai-match-controller](https://github.com/aiarena/sc2-ai-match-controller).

1. Bump the arenaclient-bot-base version in the [Dockerfile](https://github.com/aiarena/sc2-ai-match-controller/blob/master/docker/Dockerfile) file for all built containers.
2. Follow the [release](./release.md#clients) procedure for the sc2-ai-match-controller.