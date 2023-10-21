# Docker sources
This document describes manual steps with docker and docker-compose, which can later be automated with ansible scripts.

## DNS
$DOCKERDIR = ~/docker

### Basic HTTP Authentication Credentials
Create .htpasswd file in $DOCKERDIR/shared folder with username:password-hash [<gen>](https://www.web2generators.com/apache-tools/htpasswd-generator). 
This file will be used by Traefik to povide auth protection.



