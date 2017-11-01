# Definitions
build_path = build
runtime_path = runtime
build_output = ${runtime_path}/mango
runtime_fullpath = $(realpath ${runtime_path})
build_tool = runtime-container.DONE
build_number ?= none
git_commit ?= $(shell git log --pretty=oneline -n 1 -- ../mango | cut -f1 -d " ")
name = quay.io/ucsc_cgl/mango
tag = 0.0.1--${git_commit}
# docker_build_id ?= $(shell docker create ${name}-build:${tag})


# Steps
build: ${build_output} #${build_tool}

${build_output}: ${build_path}/Dockerfile
	cd ../spark-and-maven && $(MAKE) build
	cd ${build_path} && docker build -t ${name}-build:${tag} .
	$(eval docker_build_id=$(shell docker create ${name}-build:${tag}))
	@true # keeps maake from saying "nothing to be done"
	echo ${docker_build_id}
	docker cp ${docker_build_id}:/home/mango ${runtime_fullpath}
	docker cp ${docker_build_id}:/opt/apache-spark ${runtime_fullpath}
	docker rm -v ${docker_build_id}

${build_tool}: ${build_output} ${runtime_path}/Dockerfile
	cd ${runtime_path} && docker build -t ${name}:${tag} .
	-docker rmi ${name}:latest
	docker tag ${name}:${tag} ${name}:latest
	touch ${build_tool}

test: build
	python test.py

clean:
	rm -rf runtime/mango runtime/apache-spark ${build_tool}
	docker rmi -f ${name}-build:${tag} ${name}:${tag}

push: build
	# Requires ~/.dockercfg
	docker push ${name}:${tag}
	docker push ${name}:latest
