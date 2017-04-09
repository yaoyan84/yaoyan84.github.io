---
layout: post
title:  "Docker for Mac"
date:   2017-04-09 13:30:05
categories: 
tags:  docker
excerpt: 在MacOS上使用Docker。
---

## Docker安装

打开 [docker Store](https://store.docker.com/editions/community/docker-ce-desktop-mac?tab=description) 找到“Get Docker CE for Mac (Stable)”按钮，点击开始下载。

下载到dmg后，打开直接拖到应用里运行即可。具体参看同页面的说明。

![](/images/posts/2017/1-Snip20170409_9.jpg)

Mac下的软件，就是这么方便。


```
> docker

Usage:	docker COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/Volumes/MyDisk01/.docker")
  -D, --debug              Enable debug mode
      --help               Print usage
  -H, --host list          Daemon socket(s) to connect to (default [])
  -l, --log-level string   Set the logging level ("debug", "info", "warn", "error", "fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/Volumes/MyDisk01/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/Volumes/MyDisk01/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/Volumes/MyDisk01/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  checkpoint  Manage checkpoints
  container   Manage containers
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  volume      Manage volumes

Commands:
  attach      Attach to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  deploy      Deploy a new stack or update an existing stack
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.
```

## 设置

任务栏图标点击出现菜单

![](/images/posts/2017/Snip20170409_1.png)

Preferences里的设置基本能满足：

![](/images/posts/2017/Snip20170409_3.png)

![](/images/posts/2017/Snip20170409_5.png)

![](/images/posts/2017/Snip20170409_7.png)

