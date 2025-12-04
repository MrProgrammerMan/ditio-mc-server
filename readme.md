# Ditio Minecraft Server
This repo contains the configuration of the Vanilla Minecraft Server of Ditio - the student association for IT students at OsloMet.

## WIP-Disclaimer
This project is not finished and is not ready to deploy.

## Components
The server will be set up with the following components:
- A vanilla minecraft server
- A system update service
- A web server that allows:
  - Registering as a player
  - Banning for admins
  - Maintains the minecraft server state according to actions taken by web service users
- An RSync service that backs up the minecraft server
All this is packaged in a NixOS-configuration that allows the completely automatic and declarative setup of the server.

## Secrets management
Secrets are encrypted with agenix, and can only be decrypted by:
1. Action pipelines
2. The target server
The 1 exception is the private agenix key, which is only stored on the target server itself and never managed manually.

## Deployment
Deployment happens with nixos-anywhere, which sets up the initial agenix keys, restores possible latest backup from rsync if it exists, and starts the minecraft server.

### Component details
Below are some more detailed explanations of the components used in this project.

#### The minecraft server
The nixpkgs standard declarative minecraft server setup is used with the server version available in nixpkgs. This means that the whole server configuration is declarative, including whitelisted and banned players. This ensure extremely low friction in case of migration or restore.

#### The update service
This is just a nixos rebuild service. It exists to allow admins to immidiately apply bans. The rebuild also happens periodically, which is better for whitelisting. It means it takes up to 24 hours to get into the server, which acts as a safety buffer and means people can't overload the server with rebuilds because many new players join at once. There is an override machanism available to admins.

#### The web server
This is where people can register their usernames. In order to be allowed into the server, you must either:
1. login with a valid Feide account registered to a student under IT at OsloMet or
2. be manually whitelisted by an admin.
The web server is very simple, and simply uses OAuth to authenticate through feide. Moderators can ban people and force rebuild the server. Admins can rollback the server to the latest available backup.

#### The backup service
RSync runs as a service to back up the minecraft server in its entirety.

#### The deployment pipeline
The deployment pipeline requires the following repository secrets:
- Credentials for connecting to the server with root access
- Credentials for rsync
- OAuth credentials for Feide
The pipeline generates an agenix key on the client and enxrypts all other credentials using this key.

## Current state of development
See the [backlog](./backlog.md).