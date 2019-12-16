# HELP
# This will output the help for each task
# thanks to https://marmelab.com/blog/2016/02/29/auto-documented-makefile.html
.PHONY: help

help: ## This help.
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)

.DEFAULT_GOAL := help

# Variables and lists
SVC_LIST := data-api data-updater flights-api quakes-api sample-go service-tracker-ui weather-api
BLD_ALL := $(patsubst %,build-%,$(SVC_LIST))
CLN_ALL := $(patsubst %,clean-%,$(SVC_LIST))
PUB_ALL := $(patsubst %,publish-%,$(SVC_LIST))
DPL_ALL := $(patsubst %,deploy-%,$(SVC_LIST))


# Actual build and clean tasks
build: $(patsubst %,build-%,$(SVC_LIST))  ## Build all services

build-%: ## Build specified service
	@cd $(patsubst build-%,%,$(@)) ; $(MAKE) whoami build


#selective-build:
#	@echo SELECTIVE BUILD for modified stuff only
#	@MO="$(shell git show --pretty="" --name-only | egrep -v ^'users|client|shoppingcart|flights')" ; \
#	MS="$(shell git show --pretty="" --name-only | egrep ^'users|client|shoppingcart|flights'| cut -d/ -f1)" ; \
#    MSB=$(patsubst 
#	   if [ "$$MO" ]; \
#	   then echo "BUILDING ALL.."; \
#	   elif [ "$$MS" ]; \
#	   then \
#	   	echo "BUILDING SELECTIVE $$MS" ; \
#		$(MAKE) $(patsubst build-%,%,$(MS)) ; \
#	   fi;
#

clean: $(patsubst %,clean-%,$(SVC_LIST)) ## Clean all services

clean-%: ## Clean specified service
	@cd $(patsubst clean-%,%,$(@)) ; $(MAKE) clean

# Publish and release tasks
publish: $(patsubst %,publish-%,$(SVC_LIST)) ## Build and publish `latest` images to GCR

publish-%:
	cd $(patsubst publish-%,%,$(@)) ; $(MAKE) publish

# Kubernetes tasks -- all this stuff is under assumption that kubectl 
# is configured correctly outside of this Makefile

deploy: kube-init $(patsubst %,deploy-%,$(SVC_LIST))  ## Deploy all services to Kubernetes cluster
	@kubectl -n trdemo get svc,pods,deploy -o wide

deploy-%:
	cd $(patsubst deploy-%,%,$(@)) ; $(MAKE) deploy

show-images: $(patsubst %,show-img-%,$(SVC_LIST)) ## show Docker images in GCR

show-img-%:
	@X=$(patsubst show-img-%,%,$(@)); echo "\n >> DOCKER Images for $$X \n" ; cd $$X ; $(MAKE) show-image;

kube-init:	## apply Kubernetes init tasks - setup namespace, roles, etc
	kubectl apply -f devops/trdemo-namespace.yaml

