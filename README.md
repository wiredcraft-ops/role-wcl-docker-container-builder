Manage the build process of containers.

## Requirements

- Docker (duh...)

## How does it work ?

An example is better than a thousand words

**Lightweight:**

```
- hosts: builder

  pre_tasks:
    #
    # We expect the code available somewhere
    #
    - name: Fetch the latest code 
      git: 
        repo: https://gh.com/me/myrepo
        dest: /opt/myrepo

  roles:
    - role: wcl-docker-container-builder
      image_name: 'my-container'
      code_path: /opt/myrepo
      login: false
      docker_registry: "my.repo.com:5000"
```

**Complete:**

```
- hosts: builder

  pre_tasks:
    #
    # We expect the code available somewhere
    #
    - name: Fetch the latest code 
      git: 
        repo: https://gh.com/me/myrepo
        dest: /opt/myrepo

  roles:
    - role: wcl-docker-container-builder
      
      image_name: 'my-container'
      code_path: /opt/myrepo

      from_image: 'node:6.9'
      custom_tag: '1.0'
      container_type: 'strong-pm'
      container_buildargs:
          REGISTRY: https://custom.npm.org

      login: true
      artifact: true
      push: true
      push_as_latest: true

      docker_registry: "my.repo.com:5000"
      docker_username: "bob"
      docker_password: "morane"
```

## Parameters

Name | Default | Required | Description | Example
----|----|----|----|----
`image_name` | | yes | The name of the container image | 'my-container'
`code_path` | | yes | The path to the code base, depending on the `container_type` it may require a `package.json` at the root. We expect that path to be git managed as we extract the current commit hash and use it as container image tag | `/opt/myrepo`
`from_image` | 'node:6.9' | yes | The base image you will build upon you container image | 'node:6.9'
`custom_tag` | | no | Custom tag for the image | '1.0'
`container_type` | 'strong-pm' | yes | Defines the way to build the container. Refer to the `templates` folder for more details and example | 'strong-pm'
`container_buildargs` | | no | Extra parameters used to build the containers. It needs to be an object | { key: value }
login | true | no | dDfine whether it will attempt to login | true / false
artifact | true | no | Define whether we build the container image as an artifact, with multiple steps ... Don't ask for more details - check the code. | true / false
push | true | no | Define whether we push the resulting build to the registry. We will push with the current tag version (git hash) | true / false
push_as_latest | true | no | Define whether we also push the build to the registry as `latest`. Effecitvely it will overwrite the former `latest` tag was previously defined (if any). | true / false
docker_registry | | no | Only required if you want to login and want to push | 
docker_username | | no | Only required if you want to login. Effectively we will use this account to PUSH to the docker_registry, make sure this user has write access. |
docker_password | | no | Only required if you want to login. |