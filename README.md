# Local Container Registry

local_container_registry is an helper script to manage a local container registry

# Workflow

```console
# DOWNLOAD SCRIPT
$ git clone https://github.com/matteo-chesi/local_container_registry.git
Cloning into 'local_container_registry'...
...
Receiving objects: 100% (6/6), done.
$ cd local_container_registry

# SETUP the registry
$ mkdir <CHOSEN_REGISTRY_DIRPATH>
$ ./local_container_registry setup --username <CHOSEN_USERNAME> --password <CHOSEN_PASSWORD> --directory <CHOSEN_REGISTRY_DIRPATH>
Local container registry for <REGISTRY_HOST> at <CHOSEN_REGISTRY_DIRPATH> is setup.

# START the registry
# ./local_container_registry start
Local container registry started on folder <CHOSEN_REGISTRY_PATH>
The address is:
<REGISTRY_HOST>:<REGISTRY_PORT>

Run this command to use it:
podman login --tls-verify=false <REGISTRY_HOST>:<REGISTRY_PORT>

# CHECK registry status
$ ./local_container_registry status
local container registry ( use option --tls-verify=false ) is running at:
<REGISTRY_HOST>:<REGISTRY_PORT>

# LOGIN into the registry
$ podman login --tls-verify=false <REGISTRY_HOST>:<REGISTRY_PORT>
Username: <CHOSEN_USERNAME>
Password: <CHOSEN_PASSWORD>
Login Succeeded!

# USE the registry as a cache
$ cat ./Containerfile
FROM ubuntu:latest
RUN apt update
RUN apt upgrade -y

$ podman build --cache-from=<REGISTRY_HOST>:<REGISTRY_PORT>/cache --cache-to=<REGISTRY_HOST>:<REGISTRY_PORT>/cache --tls-verify=false .
STEP 1/3: FROM ubuntu:latest
STEP 2/3: RUN apt update
--> Using cache 0b3816bfbcbf80243f520570f122ebe9d0561ddd0d794f5348cb9aec7922524a
--> Pushing cache [<REGISTRY_HOST>:<REGISTRY_PORT>/cache]:3e03c073728436d9337daacb491169e6cb3868d4f0f9385058d8333dc62eb903
--> 0b3816bfbcbf
STEP 3/3: RUN apt upgrade -y
--> Using cache 455b1d38d20bc1e0c6d9f956ec1c49010065f6df310e7d26831f0d55b4431734
--> Pushing cache [<REGISTRY_HOST>:<REGISTRY_PORT>/cache]:3d19126ef52e5fd591411beca2327f085b0d05049138d3295c5703a53ca9d595
--> 455b1d38d20b
455b1d38d20bc1e0c6d9f956ec1c49010065f6df310e7d26831f0d55b4431734

# STOP the registry
$ ./local_container_registry stop
local container registry is stopped.

# CHECK registry status
$ ./local_container_registry status
local container registry is stopped.

# DELETE the registry (when data is not needed anymore)
$ ./local_container_registry delete
Removing directory <CHOSEN_REGISTRY_DIRPATH> content ... [DONE]
Removing configuration file <HOME>/.local_container_registry/<REGISTRY_HOST>_port.conf ... [DONE]
Removing configuration file <HOME>/.local_container_registry/<REGISTRY_HOST>.conf ... [DONE]

local container registry is DELETED.
```
