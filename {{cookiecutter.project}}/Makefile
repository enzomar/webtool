VERSION ?= $(shell cat VERSION)
APP=sample

.PHONY: help setup  setupdev unittest clean deta run
.DEFAULT_GOAL := help


help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

setup: venv/touchfile ## install dependencies

venv/touchfile: {{cookiecutter.project}}/requirements.txt ## install a virtual env and app requirements
	test -d venv || virtualenv venv
	. venv/bin/activate; pip install -Ur {{cookiecutter.project}}/requirements.txt
	touch venv/touchfile

setupdev: venv/touchfile.dev ## install dev dependencies

venv/touchfile.dev: {{cookiecutter.project}}/requirements-dev.txt ## install dev dependencies
	. venv/bin/activate; pip install -Ur {{cookiecutter.project}}/requirements-dev.txt
	touch venv/touchfile.dev

tests:  ## run all component tests
	@start=$$(date +%s); \
    echo $@ start: $$start > test.log 	
	- make lint; echo "lint:" [$$?] $$(date +%s) >> test.log
	- make unittest; echo "unittest:" [$$?] $$(date +%s) >> test.log
	- make coverage; echo "coverage:" [$$?] $$(date +%s)  >> test.log
	- make demo
	@end=$$(date +%s); \
    echo $@ stop: $$end >> test.log
	cat test.log

docs: setupdev ## create/update documentation
	@echo "==== $@ ===="
	. venv/bin/activate; export PYTHONPATH='./{{cookiecutter.project}}'; python -m pdoc {{cookiecutter.project}} -o docs/generated

unittest: setup setupdev ## run unitest
	@echo "==== $@ ===="
	. venv/bin/activate; cd {{cookiecutter.project}}; python -m pytest -rA ..
	
demo: setup ## run demo from source
	. venv/bin/activate; python {{cookiecutter.project}}/main.py


build: setup Dockerfile ## build docker image
	docker build -t {{cookiecutter.project}} .


run: build ## run dockerize image (read from stdin)
	docker run -i {{cookiecutter.project}}

package: setup ## create a python package under dist folder
	. venv/bin/activate; python setup.py sdist

tar: setup ## create a tar package (needs GIT repo)
	git archive --format=tar.gz -o {{cookiecutter.project}}.tar.gz HEAD -v 


deta: deta.done ## create a data micro on the default project

deta.done: 
	deta new {{cookiecutter.project}}

deploy: deta ## deploy to deta (needs deta login.)
	cd inspectphoto; ~/.deta/bin/deta deploy 

clean:
	# clean up
	rm -rf venv

