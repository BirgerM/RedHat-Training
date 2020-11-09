# Study points for the exam - EX180
This is the list of the study points for the Red Hat Certified Specialist in Containers and Kubernetes exam.
For each point ive added my comments/notes. It might be easier to read the Dockerfile reference for some of these. https://docs.docker.com/engine/reference/builder/


## Implement images using Podman
### Understand and use FROM (the concept of a base image) instruction.
The FROM instruction specifies which image to base the new image on.
This image can be a local image or one from a registry.
examples:
	FROM docker.io/roboxes/rhel8
	FROM centos8


### Understand and use RUN instruction.
The RUN instruction will run a command. This can be a single or multiple commands. To chain multiple commands in one RUN statement use &&. 
examples:
	RUN  echo "My first image"
	RUN yum install httpd -y \
	&& yum clean all


### Understand and use ADD instruction.
The ADD instruction will copy something from <src> to <dest>, but it can also copy files from an URL.
examples:
  Copy local directory: 
  ADD ./source/ /destination/

	Copy remote file:
	ADD http://example.com/remote_file.txt destination_file.txt

	Extract tar file:
	ADD ./tempfiles.tar.gz /destination/


### Understand and use COPY instruction.
The COPY instruction copies file from the source on the host to the destination.
Copy local directory:
  ADD ./source/ /destination/


### Understand the difference between ADD and COPY instructions.
ADD supports 2 other sources. First, you can use a URL instead of a local file / directory. Secondly, you can extract a tar file from the source directly into the destination.


### Understand and use WORKDIR and USER instructions.
The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile.
You can use multiple WORKDIR instructions in one single Dockerfile.


### Understand the differences and applicability of CMD vs. ENTRYPOINT instructions.
The CMD instructions specifies the command and arguments to be run in the container. You can replace the commands specified with CMD by passing a new executable and arguments when starting a container.
example:
	CMD ["executable", "param1", "param2"]
	
	To run another command than the one specified, you can pass an executable with new params when starting the container.
	podman run --name my-container -d example echo "Hello world!"

The ENTRYPOINT instruction works in the same way as the CMD, but when starting a new container the arguments provided will be passed to the entrypoint executable as arguments.
If you also want to replace the executable you will have to run podman run --entrypoint="command".

ENTRYPOINT ["executable", "param1", "param2"]


### Understand ENTRYPOINT instruction with param.
ENTRYPOINT takes an array with instructions where the first one is the executable and the other instructions are arguments to that executable.


### Understand when and how to expose ports from a Docker file.
The EXPOSE instruction doesnt really do anything, it just provides information about which ports that are used inside the container.
To expose ports you will have to pass the -p argument when starting the container. It is also possible to use -P, this will expose the ports listed with EXPOSE.


### Understand and use environment variables inside images.
The ENV instruction lets you specify key=value pairs that will be available inside the container.

example:
	This will create an environment variable called MY_STRING and then output the content of the variable into index.html.
	ENV MY_STRING="This is a tutorial"
	RUN echo $MY_STRING > /var/www/html/index.html


### Understand ENV instruction.
The ENV instruction lets you specify key=value pairs that will be available inside the container.
This can be multiple variables or just a single one.

example:
	ENV MY_STRING="This is a tutorial"
	or
	ENV MY_STRING="This is a tutorial" \
	MY_OTHER_STRING="This is a tutorial"


### Mount a host directory as a data volume.
Host directories can be mounted using the VOLUME instruction in the Dockerfile. It is also possible to mount volumes when using podman run.
--volume, -v[=[[SOURCE-VOLUME|HOST-DIR:]CONTAINER-DIR[:OPTIONS]]]

       Create a bind mount. If you specify, -v /HOST-DIR:/CONTAINER-DIR, Podman bind mounts /HOST-DIR in the host to /CONTAINER-DIR in the Podman container. Similarly, -v VOLUME-NAME:/CONTAINER-DIR will mount the volume in the host
			        to the container. If no such named volume exists, Podman will create one.


### Understand security and permissions requirements related to this approach.
It is possible to mount read-only volumes.


### Understand lifecycle and cleanup requirements of this approach.
podman volume prune will remove all unused volumes


## Manage images
Found a decent guide which explains how to use registries and so on: https://gabrieltanner.org/blog/docker-registry


### Understand private registry security.
Its possible to set up simple authentication with htpasswd. 


### Interact with many different registries.
The registries are configured in /etc/containers/registries.conf

Login to registry:
	podman login docker.io

Search for images:
	podman search rhel

Pull images:
	podman pull docker.io/roboxes/centos8

Push images:
	Before pushing images you will have to tag them correctly. You will also have to create a repository on Docker HUB if you dont already have one.
	
	Example:
		First i create my image 'example':
			podman build -t example .

		Then i check my list of images for my newly created one
		 podman images

		Before i can push the image to my repository on Docker HUB, i have to tag it with the correct tag.
			Syntax for new tag <registry>/<username>/<repository>:<tag>
				where:
					registry: docker.io
					username: birgerm
					repository: lab-repo
					tag: example

			podman tag localhost/example docker.io/birgerm/lab-repo:example

		After applying the new tag, the image can be pushed
			podman push docker.io/birgerm/lab-repo:example

		Now you can verify by running podman images and see that the REPOSITORY for the image is docker.io/<username>/<repository>


### Understand and use image tags.
Image tags are used for giving information about an image. Tags are also used to specify where the image is stored, locally, in a registry etc..


### Push and pull images from and to registries.
Pulling image:
	podman pull <image>

Pushing image:
	podman push <image>


### Back up an image with its layers and meta data vs. backup a container state.
Images can be stored using the command podman save.

Example:
	podman save example -o example.tar.gz

To load an image from a archive use podman load

Example:
	podman load -i example.tar.gz



## Run containers locally using Podman
### Get container logs.

### Listen to container events on the container host.

### Use Podman inspect.

