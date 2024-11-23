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

1. Modify the Docker.bot file in the [Docker.bot](https://github.com/aiarena/aiarena-docker-base/blob/master/docker/Dockerfile.bot) file with the relevant changes.
2. Push or PR the changes to master. Verify the CI test is successful.
3. Perform a GitHub release, tagged like so: `bot-v#.#.#` where the #'s are replaced with the version numbers. e.g. for version 1.2.3, the tag would be `bot-v1.2.3`
4. The CI will auto build and release the bot image.

## Updating the SC2 AI match controller

These steps will update the [sc2-ai-match-controller](https://github.com/aiarena/sc2-ai-match-controller).

1. Bump the arenaclient-bot-base version in the [Dockerfile](https://github.com/aiarena/sc2-ai-match-controller/blob/master/docker/Dockerfile) file for all built containers.
2. Follow the [release](./release.md#clients) procedure for the sc2-ai-match-controller.
