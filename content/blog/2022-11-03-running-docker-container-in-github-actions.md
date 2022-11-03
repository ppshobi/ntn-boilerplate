---
createdAt: 2022-11-03
title: Running Docker Container in your github actions
description: It's a pretty common need to run a docker container in a github action. Recently I was looking at one of my open source project and decided to move away from travis to github actions for my CI pipeline.
---

##  Running Docker Container in Github actions

Its a pretty common need to run a docker container in a github action. Recently I was looking at one of my [open source project](https://github.com/ppshobi/psonic) and decided to move away from Travis CI to github actions for my CI pipeline. I had to run a docker container as part of this task. 

The good thing is that the ubuntu runners comes with docker preinstalled. Windows and Mac due to [licensing of docker enterprice](https://github.com/actions/runner-images/issues/1143#issuecomment-651178811) does not come preinstalled. So you will have to do some steps on your own to install docker. 

You can have a [look here](https://github.com/actions/runner-images/issues/1143#issuecomment-652264388) its pretty straightforward.

```yaml
jobs:
  build:
    runs-on: 'macos-latest'

    steps:
      - name: Install docker
        run: |
         mkdir -p ~/.docker/machine/cache
         curl -Lo ~/.docker/machine/cache/boot2docker.iso https://github.com/boot2docker/boot2docker/releases/download/v19.03.12/boot2docker.iso
         brew install docker docker-machine
         docker-machine create --driver virtualbox default
         docker-machine env default
      - name: Run container
        run: |
         eval "$(docker-machine env default)" 
```
The above installs docker in a Mac os runner: 
 [credit](https://github.com/actions/runner-images/issues/1143#issuecomment-652264388). You can easily find such a script for windows as well online. 

 Now the remaining task is to run the docker container of your choice. Just need to add another `run` step.

```yaml
jobs:
  run:  
    runs-on: 'ubuntu-latest'
    
    steps:
    - step 1 ...
    - step 2 ... 
    
    - name: Run Sonic container
      run: docker run -d -p 1491:1491 -v ${{ github.workspace }}/sonic.cfg:/etc/sonic.cfg valeriansaliou/sonic:v1.4.0
   
    - name: See running containers
      run: docker ps
    
    - ... rest of your steps
```

you can see your running containers in your git actions log. 

see the full[ workflow here](https://github.com/ppshobi/psonic/blob/2087d0eb8bf8c3b53e19fa127fba3b6bf1c61951/.github/workflows/tests.yml).




