all: test-run

IMAGE_NAME = cpp-ci
IMAGE_VER = v1.0.0
CONTAINER_NAME = cpp-ci
CONTAINER_USER = op1
MAPPING_SSH_PORT = 32012
DOCKERFILE_PATH = $(PWD)/Dockerfile
DOCKER_BUILD_DIR = $(PWD)
SLEEP_SECS=2
MAPPING_HOST_PATH = ~/project_mapping
MAPPING_CONTAINER_PATH = /home/$(CONTAINER_USER)/project_mapping

test-run:
	echo "test"   

docker-build:
	sudo docker build -f $(DOCKERFILE_PATH) -t $(IMAGE_NAME):$(IMAGE_VER) $(DOCKER_BUILD_DIR)

docker-ps:
	sudo docker ps -f name=$(CONTAINER_NAME)

docker-run:
	chmod 777 $(MAPPING_HOST_PATH)
	sudo docker run -d -p $(MAPPING_SSH_PORT):22 --privileged=true -v $(MAPPING_HOST_PATH):$(MAPPING_CONTAINER_PATH) --name $(CONTAINER_NAME) $(IMAGE_NAME):$(IMAGE_VER)
	sleep $(SLEEP_SECS)
	sudo docker ps -f name=$(CONTAINER_NAME)
	sudo docker logs $(CONTAINER_NAME)

docker-start:
	sudo docker start $(CONTAINER_NAME)
	sleep $(SLEEP_SECS)
	sudo docker ps -f name=$(CONTAINER_NAME)

docker-stop:
	sudo docker stop $(CONTAINER_NAME)

docker-rm:
	sudo docker stop $(CONTAINER_NAME)
	sudo docker rm $(CONTAINER_NAME)

docker-rmi-repo:
	sudo docker rmi $(IMAGE_NAME)

docker-rmi-repo-tag:
	sudo docker rmi $(IMAGE_NAME):$(IMAGE_VER)


.PHONY: docker-build docker-ps docker-run docker-start docker-stop docker-rm docker-rmi-repo docker-rmi-repo-tag build-daemon-app
