# Deploy-Docker-image-through-Gitlab-CI-CD 

Here we are using `ubuntu Linux x86-64` for this advanture.


We have followed the following link to install the Gitlab CI CD Runner,

https://docs.gitlab.com/runner/install/linux-manually.html

We will download and install via binary file

1.Simply download one of the binaries for your system:

> $ sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64 

2. Give it permissions to execute:

> $ sudo chmod +x /usr/local/bin/gitlab-runner 


3.Create a GitLab CI user:

> $ sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash 

4.Install and run as service:

> $ sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
> $ sudo gitlab-runner start


Register the Runner:

To register a runner under GNU/LInux,

1. Run the following command:

> $ sudo gitlab-runner register 

2. Enter your GitLab instance URL:

> $ Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
> https://gitlab-rec.com/


3. Enter the token you obtained to register the Runner:

> $ Please enter the gitlab-ci token for this runner
> KK69Lde9PtFTzU7UCxHs


Find Runner set up instructions at admin area>Runners.

You can also find it from project left side bar’s by expanding settings and choosing CI CD section. Then expand the Runner option. 

If you don’t see the CI CD option under project setting, you have to enable it from `admin area> setting> CI CD`. Expand the Continuous Integration and Deployment. Check the Enable shared runners for new projects option and check Default to Auto DevOps pipeline for all projects option. Then click the save changes button. It it is still invisible, then go to project `settings> general> Visibility`, project features, permissions(Expand)> check pipeline option. CI CD will be visible for this project. 



4. Enter a description for the Runner, you can change this later in GitLab’s UI:

> $ Please enter the gitlab-ci description for this runner
> $ [hostname] my-runner



5. Enter the tags associated with the Runner, you can change this later in GitLab’s UI:

Please enter the gitlab-ci tags for this runner (comma separated):

`My-tag,another-tag`

6. Enter the Runner executor:

```
$ Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
$ shell
```

A message will be displayed as follows `“ Runner registered successfully. Feel free to start it, but if it's running already the config should be automatically reloaded!”`

Then start the runner again,

> $ sudo gitlab-runner start 

Now verify that runner is running or not,

Go to project left side bar’s by expanding settings and choosing CI CD section. Then expand the Runner option. You will discover the runner and its running status. 


Now we have to make `.gitlab-ci.yml` to execute the pipeline. Create the file via nano command or gitlab create new file with proper .yml extension.

Here is the `.gitlab-ci.yml` file.

```
# This file is a template, and might need editing before it works on your project.
# see https://docs.gitlab.com/ce/ci/yaml/README.html for all available options

# you can delete this line if you're not using Docker
#image: busybox:latest

before_script:
  - echo "Before script section"
  - echo "For example you might run an update here or install a build dependency"
  - echo "Or perhaps you might print out some debugging details"

after_script:
  - echo "After script section"
  - echo "For example you might do some cleanup here"

build1:
  stage: build
  script:
    - echo "Do your build here"
    - cd /home/akash
    - sudo docker-compose up -d
    - sudo docker ps -a

test1:
  stage: test
  script:
    - echo "Do a test here"
    - echo "For example run a test suite"

test2:
  stage: test
  script:
    - echo "Do another parallel test here"
    - echo "For example run a lint test"

deploy1:
  stage: deploy
  script:
    - echo "Do your deploy here"
```

If the file creation or push is done, the pipeline will automatically start the job.

# Troubleshooting

[We had experienced a different error `“bash: line 86: cd: /home/gitlab-runner/builds/strryoym/0/akash/gitlab_cicd: No such file or directory 00:00 ERROR: Job failed: exit status 1”`. To solve this, we went to the gitlab-runner folder. 

> $ cd /home/gitlab-runner/
> $ ls -a
> $ sudo nano .bash_logout

Then commented on all lines which were not commented on.]

Our pipeline job was to run a `docker-compose.yml` file. We scripted all commands with sudo. But in the inner terminal it was asking for user password. To resolve this we edited visudo with the following command, 

> $ sudo visudo 

Then we added the following file.

`gitlab-runner ALL=(ALL:ALL) NOPASSWD: ALL`

The problem was solved and the job was successful. The docker compose file will be found on this directory.


Here is the `docker-compose.yml` file.

```
version: '3'
services:

 helloworld:
   image: ubuntu:14.04
   command: /bin/echo 'Hello world'
```
