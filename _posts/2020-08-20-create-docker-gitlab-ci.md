---
title:  "How To Create a Docker Image during GitLab Pipeline"
date:   2020-08-20 
badges: 
 - type: info
   tag: k8s 
tags:
 - gitlab-pipeline
 - docker
---

Use:
* image `docker:dind`
* selector: `tags: - kubernetes`
* docker daemon detached `dockerd &`

Example:

```
create_docker_image:
    image: docker:dind
    stage: build
    script:
        - dockerd &
        - sleep 5
        - docker build -t <registry>/docs:1.0.0 --no-cache .
    only:
        - master
    tags:
        - kubernetes
```
