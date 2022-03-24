---
title: "Docker MySQL Refuse Connection"
description: "It's like baby refusing to eat..."
date: 2022-03-25T00:59:16+08:00
draft: false
toc: false
images:
cover: "/posts/docker-mysql-refuse-connection/images/facade.jpg"
tags:
  - coding
---
The entire day I was working on running a CodeIgniter project using Docker, and it was exhausting since *Dockerization* is still new to me. 

The issue came aroused when the application (be it Laravel, CI, or even vanilla) trying to establish a connection with the database within Docker.

## The Moment of Truth
Just after nearly an hour on the error, I remembered while working with Laravel Sail, it too has the same issue.

The MySQL connection **must** be based on the service name defined for the database container.

```yaml
services:
    mysql:
```
Above code snippet shows the service name of the database container is `mysql`. So, replace your host from either `localhost` or `127.0.0.1` (in some cases, `0.0.0.0`) with `mysql` instead.

```php
'host' => 'mysql'
```

It should fix the refuse problem. Good luck!