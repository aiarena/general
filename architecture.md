---
layout: default
title: Architecture
nav_order: 301
---

# Architecture

This is a description of the architecture of AI Arena.

![Entities in AI Arena](images/architecture-entities.png)

AI Arena works with the following entities:
* **Bots** - Bots compete in the arena in various competitions. Each bot has a unique identifier, a name, biography, one code bundle (called *Bot Zip*), and one data bundle (called *Bot data*) to pass internal information along its matches.
* **Authors** - Bot authors create bots and enroll them in competitions.
* **Spectators** - Spectators are people who enjoy watching bots play games.
* **Patreons** - Patreons are bot authors and spectators who give financial support to AI Arena. AI Arena is free to watch and play, it is not pay-to-win, but patreons enjoy additional benefits like higher limits for match requests.
* **Games** - There are different games, like StarCraft II and Poker, the bots can play.
* **Competitions** - Competitions are organized by AI Arena for many bots to play one game. Each competition has rules, open and close dates, rounds and rankings. It has game-specific properties, like maps in StarCraft II.
* **Rankings** - The bots participating in a competition are ranked by their ELO rating within the competition and are split into divisions according to the rules of the competition. Competitions usually start with a single division and ELO of 1600 for all bots. Bots may join and leave a competition between any two rounds and joining bots start with ELO rating of 1600.
* **Rounds** - Competitions are played in rounds where each bot plays one match against each other bot in the same division of the rankings.
* **Matches** - A match is played by two bots. Matches are either started within a round of an active competition, or manually requested by a bot author or a spectator. Matches of a competition affect the ELO rating for the participating bots for this competition. Manually requested matches don't affect the ELO rating of the participating bots.
* **Logs** - Matches produce logs for both bots for the bot authors to inspect and improve their bot. The logs for a bot are accessible only to their bot author.
* **Replays** - Matches produce game replays. Replays are publicly accesible. They are watched by bot authors to improve their bots, and by spectators for fun.
* **Stream** - Replays of games like StarCraft II requires additional software to play, so AI Arena streams matches for easier viewing.
* **Communities** - Bot authors and spectators usually come to AI Arena from communities that are interested in bots playing games. AI Arena links back to the communities and feeds content like bot performance and competition results back to the communities.

# Systems

![Logical systems](images/architecture-systems.png)

- Website - The website is the frontend to AI Arena. It contains a Web site for users and API endpoints for tools.
- Database - The database contains the records for bots, authors, games, competitions, rankings, rounds, and matches.
- Content delivery system - Stores and provides access to the replays and logs of matches.
- Clients - Plays requested matches by running 2 bots on 1 game. The client fetches the input match information from the API of the website and feeds the output match information back to the API.
    - K8S Controller fetches the queued matches for the configured number of clients. It initializes one Arena Client for a match ensuring there's only one active Arena Client with the same number at any time.
    - Arena Client runs the match given by the K8S Controller.
        - Proxy Controller is the main controller in the client. It is responsible for fetching and starting games. It is also an intermediate between the other controllers and the caching server.
        - SC2 Controller starts the StarCraft II game with the configuration given by the Proxy Controller.
        - Bot Controller is starts one bot with the configuration given by the Proxy Controller. There are 2 instances of this controller in the client.
    - Caching Service acts as an intermediate between the client and the Frontend API. It caches the files downloaded from the Frontend API to reduce inbound traffic.
- Streaming service - Streams match replays for users.
- Stream - Converts a replay to the media format of the streaming service and feeds it.
- Wiki - Contains information about AI Arena, the games, competitions and their rules, instructions and guides for bot authors, etc.
- Patreon service - Used for handling donations from patreons.

# Technologies

* AgroCD - 
* AWS - Used as cloud infrastructure for the AI Arena frontend.
* Docker - 
* GitHub - Stores the source code for AI Arena.
* Hetzner Cloud - Used as cloud infrastructure for the AI Arena clients and stream.
* Kubernetes - Used for hosting AI Arena Clients.
* Patreon - Used for the patreon service.
* PostgreSQL - Used for the database. Version 15. Managed service by AWS.
* Rancher - Controls the Kubernetes cluster
* Twitch - Used for the streaming service.
* Wiki

## Clients

AI Arena Clients run in a Kubernetes cluster controlled by Rancher.
The source code and configuration is maintained in GitHub at [github.com/aiarena/sc2-ai-match-controller](https://github.com/aiarena/sc2-ai-match-controller) and [github.com/aiarena/AiarenaCachingServer](https://github.com/aiarena/AiarenaCachingServer).
All components are built as Docker images.

K8S Controller is a Kubernetes **deployment** deployed using a [Kustomize descriptor](https://github.com/aiarena/sc2-ai-match-controller/tree/master/kubernetes). The resulting Kubernetes **pod** fetches the list of queued matches from AI Arena API and starts an Arena Client as Kubernetes **jobs** for each match but only one per configured AI Arena client at a time. On completion of the job, the controller uploads the match result to AI Arena API.

Arena Client is a Kubernetes **job**. The resulting Kubernetes **pod** has 4 containers - a proxy contoller, an SC2 controller, and 2 bot controllers.

Caching Server is a Kubernetes **deployment** deployed manually. It uses the file system of the Docker container to store cached files.
