python = python2.7
SHELL=/bin/bash
DOCKER_HELPER := $(shell dirname $(abspath $(lastword $(MAKEFILE_LIST))))/docker_helper.sh

ROLE_VERSION=$(shell cat $(PWD)/../version.txt)
ROLE_NAME=$(shell . $(DOCKER_HELPER) ; getUpDirLevel2)

REGISTRY_HOST=docker.io
USERNAME=softasap

all: clean_build

clean_build: clean initialize build

build:  p-env/bin/ansible-container custom_roles
	@echo p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) build --roles-path ./roles/ -- -vvv
	@p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) build --roles-path ./roles/ -- -vvv
	@rm -rf custom-roles || true
	@echo "Application docker image was build"

run:  p-env/bin/ansible-container
	@echo p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) run --roles-path ./roles/ -- -vvv
	@p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) run --roles-path ./roles/ -- -vvv
	@echo "Application environment was started"

stop:   p-env/bin/ansible-container
	@echo p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) stop
	@p-env/bin/ansible-container --debug --project-name $(ROLE_NAME) stop
	@echo "Application environment was stopped"

p-env/bin/pip: p-env/bin/python
	p-env/bin/pip install -r requirements.txt

p-env/bin/python:
	virtualenv -p $(python) --no-site-packages p-env
	@touch $@

p-env/bin/ansible-container: p-env/bin/pip
	@touch $@

custom_roles:
	cp -r ../custom-roles .

clean:
	@rm -rf .Python p-env roles

initialize:
	@init_quick.sh

tag:
	@docker tag $(ROLE_NAME)-$(SERVICE_NAME):latest softasap/sa-container-box-examples:$(ROLE_NAME).$(SERVICE_NAME).$(ROLE_VERSION)
	@docker tag $(ROLE_NAME)-$(SERVICE_NAME):latest softasap/sa-container-box-examples:$(ROLE_NAME).$(SERVICE_NAME).latest

push:
	@docker push softasap/sa-container-box-examples:$(ROLE_NAME).$(SERVICE_NAME).$(ROLE_VERSION)
	@docker push softasap/sa-container-box-examples:$(ROLE_NAME).$(SERVICE_NAME).latest

sh-conductor:
	until [ "`/usr/bin/docker inspect -f {{.State.Running}} $(ROLE_NAME)_conductor`"=="true" ]; do \
	sleep 0.1; \
        echo "Waiting for $(ROLE_NAME)..." \
	done; \
	docker exec -it $(ROLE_NAME)_conductor /bin/sh

.PHONY: all clean initialize build run stop

