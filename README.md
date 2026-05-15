# Local Container Registry

local_container_registry manages a local container registry

it can run a local container registry inside a slurm job listening on localhost 

It provides:
* env-registry  - script to be sourced in order to add its folder to $PATH
* registry      - script to manage local registry server
* podman-cached - wrapper for "podman build", add those options that enable cache on local registry

# Workflow

```console
# DOWNLOAD 
$ git clone https://github.com/matteo-chesi/local_container_registry.git
Cloning into 'local_container_registry'...
...
Receiving objects: 100% (6/6), done.
$ cd local_container_registry

# LAUNCH interactive slurm job
$ srun ... --pty bash

# SETUP the registry
$ mkdir <CHOSEN_REGISTRY_DIRPATH>

# <CHOSEN_REGISTRY_DIRPATH> can be omitted if unchanged 
$ . env-registry && registry up <CHOSEN_REGISTRY_DIRPATH>
Local container registry started on folder <CHOSEN_REGISTRY_DIRPATH>
The address is:
<REGISTRY_HOST>:<REGISTRY_PORT>

# USE the registry as a cache
$ cat ./Containerfile
FROM ubuntu:latest
RUN apt update
RUN apt upgrade -y

$ podman-cached .
Executing: podman build --layers --tls-verify=false --cache-from=<REGISTRY_HOST>:<REGISTRY_PORT>/cache -<REGISTRY_HOST>:<REGISTRY_PORT>/cache .
STEP 1/3: FROM ubuntu:latest
STEP 2/3: RUN apt update
--> Using cache 7e878891345baea142da9dca42bf131488a317178a162b1388f449f40328eb45
--> Pushing cache [localhost:5000/cache]:3e03c073728436d9337daacb491169e6cb3868d4f0f9385058d8333dc62eb903
--> 7e878891345b
STEP 3/3: RUN apt upgrade -y
--> Using cache 2c8781e264b03ce41c571b6a4d24c8d8472a413ee50073ad662e517f92a0ff2a
--> Pushing cache [localhost:5000/cache]:3e44a53a47cd2318f910222fec5ae4f68853c471410d73f52e6b01c4eaec5555
--> 2c8781e264b0
2c8781e264b03ce41c571b6a4d24c8d8472a413ee50073ad662e517f92a0ff2a

# STOP the registry
$ registry down
local container registry is stopped.
```

# Additional commands

```console
# CHECK registry status (UP)
$ registry status
local container registry is running at:
<REGISTRY_HOST>:<REGISTRY_PORT>

# CHECK registry status (DOWN)
$ registry status
local container registry is stopped.

# DELETE the registry (when data is not needed anymore)
$ registry delete
Removing directory <CHOSEN_REGISTRY_DIRPATH> content ... [DONE]
Removing configuration file <HOME>/.local_container_registry/registry.conf ... [DONE]

local container registry is DELETED.
```
