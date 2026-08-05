all: test-run

test-run:
	echo "test"  

DOCKER_BUILD_DIR = $(PWD)
CONTAINERD_SOCK = /run/k3s/containerd/containerd.sock

# CPP-CI Image
IMG_CI_NAME = cpp-ci
IMG_CI_VER = v1.0.0
CI_DOCKERFILE_PATH = $(PWD)/Dockerfile.CI

ci-build:
	sudo docker build -f $(CI_DOCKERFILE_PATH) -t $(IMG_CI_NAME):$(IMG_CI_VER) $(DOCKER_BUILD_DIR)
	docker save $(IMG_CI_NAME):$(IMG_CI_VER) > $(IMG_CI_NAME)-$(IMG_CI_VER).tar
	sudo ctr --address $(CONTAINERD_SOCK) --namespace k8s.io images import $(IMG_CI_NAME)-$(IMG_CI_VER).tar
	rm $(IMG_CI_NAME)-$(IMG_CI_VER).tar

ci-rmi-repo:
	sudo docker rmi $(IMG_CI_NAME)
	
ci-rmi-repo-tag:
	sudo docker rmi $(IMG_CI_NAME):$(IMG_CI_VER)


# CPP-Compiler Image
IMG_COMPILER_NAME = cpp-compiler
IMG_COMPILER_VER = v1.0.0
IMG_COMPILER_DOCKERFILE_PATH = $(PWD)/Dockerfile.Compiler

compiler-build:
	sudo docker build -f $(IMG_COMPILER_DOCKERFILE_PATH) -t $(IMG_COMPILER_NAME):$(IMG_COMPILER_VER) $(DOCKER_BUILD_DIR)
	docker save $(IMG_COMPILER_NAME):$(IMG_COMPILER_VER) > $(IMG_COMPILER_NAME)-$(IMG_COMPILER_VER).tar
	sudo ctr --address $(CONTAINERD_SOCK) --namespace k8s.io images import $(IMG_COMPILER_NAME)-$(IMG_COMPILER_VER).tar
	rm $(IMG_COMPILER_NAME)-$(IMG_COMPILER_VER).tar

compiler-rmi-repo:
	sudo docker rmi $(IMG_COMPILER_NAME)

compiler-rmi-repo-tag:
	sudo docker rmi $(IMG_COMPILER_NAME):$(IMG_COMPILER_VER)


.PHONY: ci-build ci-rmi-repo ci-rmi-repo-tag compiler-build compiler-rmi-repo compiler-rmi-repo-tag
