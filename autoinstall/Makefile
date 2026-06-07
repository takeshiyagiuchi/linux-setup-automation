# https://cross-black777.hatenablog.com/entry/2015/02/23/214337
#
# This is supposed to run on MacOS
# *required commands: wget,
#

VAR_FILE = variables.txt

UBUNTU_VERSION_MJR = $(shell cat $(VAR_FILE) | grep "UBUNTU_VERSION_MJR" | awk -F'=' '{print $$2}')
UBUNTU_VERSION_MDL = $(shell cat $(VAR_FILE) | grep "UBUNTU_VERSION_MDL" | awk -F'=' '{print $$2}')
UBUNTU_VERSION_MNR = $(shell cat $(VAR_FILE) | grep "UBUNTU_VERSION_MNR" | awk -F'=' '{print $$2}')
TARGET_MACHINE_HN = $(shell cat $(VAR_FILE) | grep "TARGET_MACHINE_HN" | awk -F'=' '{print $$2}')
TARGET_MACHINE_IP = $(shell cat $(VAR_FILE) | grep "TARGET_MACHINE_IP" | awk -F'=' '{print $$2}')
TARGET_MACHINE_MC = $(shell cat $(VAR_FILE) | grep "TARGET_MACHINE_MC" | awk -F'=' '{print $$2}')
TARGET_MACHINE_MC_s = $(shell echo ${TARGET_MACHINE_MC} | tr '[A-Z]' '[a-z]')
WORK_DIR = $(shell cat $(VAR_FILE) | grep "WORK_DIR" | awk -F'=' '{print $$2}')

TO_DOCKER_PATH = to_docker

ISO_ORG_FILENAME = ubuntu-$(UBUNTU_VERSION_MJR).$(UBUNTU_VERSION_MDL).$(UBUNTU_VERSION_MNR)-live-server-amd64.iso
ISO_ORG_URL = https://releases.ubuntu.com/$(UBUNTU_VERSION_MJR).$(UBUNTU_VERSION_MDL)/$(ISO_ORG_FILENAME)

USERDATA_PATH = to_docker/config/user-data
USERDATA_COMMON_PATH = to_docker/config/user-data_per_machine/common/user-data
NWCONFIG_PATH_ORG = to_docker/config/post-processing/original/00-installer-config.yaml
NWCONFIG_PATH = to_docker/config/post-processing/00-installer-config.yaml

DOCKERFILE = Dockerfile
DOCKER_MAKEFILE = to_docker/Makefile

DOCKER_IMAGE_NAME = ubuntu-iso-maker
DOCKER_CONTAINER_NAME = ubuntu-iso-maker

DOCKER_PS = $(shell docker ps -f name=$(DOCKER_CONTAINER_NAME) | wc -l)

ISO_FILENAME = ubuntu$(UBUNTU_VERSION_MJR)$(UBUNTU_VERSION_MDL)$(UBUNTU_VERSION_MNR)-$(TARGET_MACHINE_HN).iso

all:
	$(MAKE) preprocess
	$(MAKE) download-iso
	$(MAKE) build-dockerimage
	$(MAKE) run-container
	$(MAKE) copy-iso
	$(MAKE) clean

.PHONY: preprocess
preprocess:
	# Edit Dockerfile
	sed -i '' -E 's/^ENV WORK_DIR=.*/ENV WORK_DIR=\$(WORK_DIR)/g' $(DOCKERFILE)
	sed -i '' -E 's/^COPY $(TO_DOCKER_PATH)\/ubuntu-.*/COPY $(TO_DOCKER_PATH)\/$(ISO_ORG_FILENAME) $$WORK_DIR\//g' $(DOCKERFILE)

	# Edit Makefile on container
	sed -i '' -E 's/^UBUNTU_VERSION_MJR =.*/UBUNTU_VERSION_MJR = $(UBUNTU_VERSION_MJR)/g' $(DOCKER_MAKEFILE)
	sed -i '' -E 's/^UBUNTU_VERSION_MDL =.*/UBUNTU_VERSION_MDL = $(UBUNTU_VERSION_MDL)/g' $(DOCKER_MAKEFILE)
	sed -i '' -E 's/^UBUNTU_VERSION_MNR =.*/UBUNTU_VERSION_MNR = $(UBUNTU_VERSION_MNR)/g' $(DOCKER_MAKEFILE)
	sed -i '' -E 's/^TARGET_MACHINE_HN =.*/TARGET_MACHINE_HN = $(TARGET_MACHINE_HN)/g' $(DOCKER_MAKEFILE)
	sed -i '' -E 's/^TARGET_MACHINE_IP =.*/TARGET_MACHINE_IP = $(TARGET_MACHINE_IP)/g' $(DOCKER_MAKEFILE)
	sed -i '' -E 's/^GENISO_FILENAME =.*/GENISO_FILENAME = $(ISO_FILENAME)/g' $(DOCKER_MAKEFILE)

	# Edit user-data
	cp -f $(USERDATA_COMMON_PATH) $(USERDATA_PATH)
	cat $(USERDATA_PATH) | grep "TARGET_MACHINE"
	sed -i '' -E 's/.*TARGET_MACHINE_HN_hostname$$/    hostname: $(TARGET_MACHINE_HN) # TARGET_MACHINE_HN_hostname/g' $(USERDATA_PATH)
	sed -i '' -E 's/.*TARGET_MACHINE_HN_realname$$/    realname: $(TARGET_MACHINE_HN) # TARGET_MACHINE_HN_realname/g' $(USERDATA_PATH)
	sed -i '' -E 's/.*TARGET_MACHINE_IP$$/        - $(TARGET_MACHINE_IP)\/24 # TARGET_MACHINE_IP/g' $(USERDATA_PATH)
	sed -i '' -E 's/.*TARGET_MACHINE_MC$$/          macaddress: $(TARGET_MACHINE_MC) # TARGET_MACHINE_MC/g' $(USERDATA_PATH)
	sed -i '' -E 's/^      ip link \| grep -n "[^"]*"/      ip link | grep -n "$(TARGET_MACHINE_MC_s)"/g' $(USERDATA_PATH)
	cat $(USERDATA_PATH) | grep "TARGET_MACHINE"

	# Edit netplan config
	cp -f $(NWCONFIG_PATH_ORG) $(NWCONFIG_PATH)
	sed -i '' -E 's/^      - 192.168.0.1x\/24$$/      - $(TARGET_MACHINE_IP)\/24/g' $(NWCONFIG_PATH)
	sed -i '' -E 's/^        macaddress:.*/        macaddress: $(TARGET_MACHINE_MC)/g' $(NWCONFIG_PATH)

.PHONY: download-iso
download-iso:
	wget -P $(TO_DOCKER_PATH) -nc $(ISO_ORG_URL);

.PHONY: build-dockerimage
build-dockerimage:
	docker build -t $(DOCKER_IMAGE_NAME) ./

.PHONY: run-container
run-container:
	docker ps -a
	if [ $(DOCKER_PS) -gt "1" ]; then \
        docker stop $(DOCKER_CONTAINER_NAME) && docker rm $(DOCKER_CONTAINER_NAME); \
        docker ps -a; \
    fi
	docker run --rm -itd --name $(DOCKER_CONTAINER_NAME) $(DOCKER_IMAGE_NAME)
	docker ps -a

.PHONY: copy-iso
copy-iso:
	docker cp $(DOCKER_CONTAINER_NAME):$(WORK_DIR)/$(ISO_FILENAME) ./

.PHONY: clean
clean:
	docker stop $(DOCKER_CONTAINER_NAME)
	docker ps -a
	docker image rm $(DOCKER_CONTAINER_NAME)
	docker images
# end