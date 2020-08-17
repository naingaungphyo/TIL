## [Skaffold](https://www.skaffold.dev)

Skaffold handles the workflow for building, pushing and deploying your application, allowing you to focus on what matters most: writing code.

Example of skaffold config file:

```
apiVersion: skaffold/v2alpha3
kind: Config
deploy:
kubectl:
  # files to watch for changes
  manifests:
    - ./infra/k8s/*
build:
local:
  # if we change to one of our images or rebuild an image,
  # that's going to try to push it up to DockerHub.
  # if you don't need that default feature, you can turn it off.
  push: false
artifacts:
  # if there is a change except sync: manual: src: files, rebuild entire image
  - image: yourdockerid/client
    context: client
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: 'src/**/*.js'
          dest: .
  - image: yourdockerid/comments
    context: comments
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: '*.js'
          dest: .
  - image: yourdockerid/event-bus
    context: event-bus
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: '*.js'
          dest: .
  - image: yourdockerid/moderation
    context: moderation
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: '*.js'
          dest: .
  - image: yourdockerid/posts
    context: posts
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: '*.js'
          dest: .
  - image: yourdockerid/query
    context: query
    docker:
      dockerfile: dockerfile
    sync:
      # if we change src: files, skaffold will update new file directly to the pods
      manual:
        - src: '*.js'
          dest: .
```
