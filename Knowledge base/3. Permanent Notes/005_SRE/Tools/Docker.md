
Delete docker image

`docker image ls | awk '{print $3}' | sed 1d | xargs docker rmi --force {} \\+`

Delete docker container

`docker ps -a | awk '{print $1}' | sed 1d | xargs docker rm {}`