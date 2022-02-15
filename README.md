# Maxx-4uTeP_microservices
Maxx-4uTeP microservices repository

yc compute instance create \
  --name docker-host \
  --zone ru-central1-a \
  --network-interface subnet-name=default-ru-central1-a,nat-ip-version=ipv4 \
  --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-1804-lts,size=15 \
  --ssh-key ~/.ssh/id_rsa.pub
	
docker-machine create \
  --driver generic \
  --generic-ip-address=84.252.131.147 \
  --generic-ssh-user yc-user \
  --generic-ssh-key ~/.ssh/id_rsa \
  docker-host
  
eval $(docker-machine env docker-host)


docker build -t reddit:latest .

docker run --name reddit -d --network=host reddit:latest

docker-machine ls

docker login

docker tag reddit:latest 4utep/otus-reddit:1.0

docker push 4utep/otus-reddit:1.0

docker run --name reddit -d -p 9292:9292 4utep/otus-reddit:1.0



docker network create reddit
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post 4utep/post:1.0
docker run -d --network=reddit --network-alias=comment 4utep/comment:1.0
docker run -d --network=reddit -p 9292:9292 4utep/ui:1.0

docker build -t 4utep/ui:2.0 ./ui

docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post 4utep/post:1.0
docker run -d --network=reddit --network-alias=comment 4utep/comment:1.0
docker run -d --network=reddit -p 9292:9292 4utep/ui:2.0


docker volume create reddit_db


docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db -v reddit_db:/data/db mongo:latest
docker run -d --network=reddit --network-alias=post 4utep/post:1.0
docker run -d --network=reddit --network-alias=comment 4utep/comment:1.0
docker run -d --network=reddit -p 9292:9292 4utep/ui:2.0


# docker-4

docker network create reddit --driver bridge

Скачаем последний образ MongoDB:
docker pull mongo:latest

Соберем образы с нашими сервисами:
docker build -t 4utep/post:1.0 ./post-py 
docker build -t 4utep/comment:1.0 ./comment 
docker build -t 4utep/ui:1.0 ./ui


docker run -d --network=reddit mongo:latest
docker run -d --network=reddit 4utep/post:1.0
docker run -d --network=reddit 4utep/comment:1.0
docker run -d --network=reddit -p 9292:9292 4utep/ui:1.0

docker kill $(docker ps -q)

docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post 4utep/post:1.0
docker run -d --network=reddit --network-alias=comment 4utep/comment:1.0
docker run -d --network=reddit -p 9292:9292 4utep/ui:1.0

# 18

docker kill $(docker ps -q)

docker network create back_net --subnet=10.0.2.0/24
docker network create front_net --subnet=10.0.1.0/24

# 19

docker run -d --network=front_net -p 9292:9292 --name ui 4utep/ui:1.0 
docker run -d --network=back_net --name comment 4utep/comment:1.0
docker run -d --network=back_net --name post 4utep/post:1.0
docker run -d --network=back_net --name mongo_db --network-alias=post_db --network-alias=comment_db mongo:latest

# 21

docker network connect front_net post
docker network connect front_net comment

# 23
docker-machine ssh docker-host
sudo apt-get update && sudo apt-get install bridge-utils
sudo docker network ls

          NETWORK ID     NAME        DRIVER    SCOPE
          eae3c7059254   back_net    bridge    local
          9c0a9ab6ff98   bridge      bridge    local
          c83249e9df6b   front_net   bridge    local
          760f6f834b23   host        host      local
          4b07721f153c   none        null      local
          8c6ae3a2f0fd   reddit      bridge    local

# 24

ifconfig | grep br
brctl show br-eae3c7059254
              bridge name     bridge id               STP enabled     interfaces
              br-eae3c7059254         8000.0242a9b2bd6c       no              veth3b07b97
                                                                      veth6c3c445
                                                                      vetha2e934a
# 25

sudo iptables -nL -t nat

# 26
ps ax | grep docker-proxy

# 33
docker kill $(docker ps -q)

export USERNAME=4utep
docker-compose up -d
docker-compose ps









