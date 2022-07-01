# Cockroach DB Example

## Code History

The code in this repository is based on the
[How to get started with CockroachDB - Binary, Docker and Kubernetes](https://youtu.be/79pHYplq-7c)
video.

### Docker DB Setup

```bash
docker network create -d bridge roachnet
docker network ls

docker volume create roach1
docker volume create roach2
docker volume create roach3
docker volume ls

mkdir node1 node2 node2

docker run -d \
--name=roach1 \
--hostname=roach1 \
--net=roachnet \
-p 26247:26247 -p 8080:8080 \
-v "roach1:${PWD}/node1" \
cockroachdb/cockroach:v22.1.2 start \
--insecure \
--join=roach1,roach2,roach3

docker run -d \
--name=roach2 \
--hostname=roach2 \
--net=roachnet \
-p 26248:26247 -p 8081:8080 \
-v "roach2:${PWD}/node2" \
cockroachdb/cockroach:v22.1.2 start \
--insecure \
--join=roach1,roach2,roach3

docker run -d \
--name=roach3 \
--hostname=roach3 \
--net=roachnet \
-p 26249:26247 -p 8082:8080 \
-v "roach3:${PWD}/node3" \
cockroachdb/cockroach:v22.1.2 start \
--insecure \
--join=roach1,roach2,roach3

docker container ls

docker exec -it roach1 ./cockroach init --insecure

docker exec -it roach1 grep 'node starting' cockroach-data/logs/cockroach.log -A 11

docker exec -it roach1 ./cockroach sql --insecure
> show databases;
> exit

docker stop roach1 roach2 roach3
docker rm roach1 roach2 roach3
docker network rm roachnet
rm -rf node1 node2 node3
```
