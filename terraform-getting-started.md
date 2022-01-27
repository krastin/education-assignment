# Getting Started with Terraform

Terraform is a tool for defining and provisioning Infrastructure as Code (IaC).
In this guide, you will learn the basics of Terraform by provisioning a single Docker container running the nginx web server.

## Prerequisites

This tutorial assumes that you have a minimal knowledge of Docker and have it up and running.

- Get Docker and learn how to install it [here](https://docs.docker.com/get-docker/)

## Getting Terraform

To install Terraform, download the appropriate package for your system from [Terraform.io](https://www.terraform.io/downloads.html).

With Terraform installed, you are ready to create some infrastructure.

## Terraform Configuration and Initialization

We recommend creating a new directory on your local machine for keeping the Terraform configuration code inside.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize Terraform with the `init` command. Next, Terraform will install the Docker provider. 

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.16.0...
- Installed kreuzwerker/docker v2.16.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## Create infrastructure

With Terraform initialized successfully, provision the resources with the `apply` command. The command asks for confirmation at the bottom of the output. Type `yes` and hit ENTER.

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 8s [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=5bd271839b6581c0d271df0be9123ca7cc0129e41ed3f0a00268c0a4d841fb03]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

The `apply` command takes a while to run and displays a message indicating the resources were created.

If the command shows an error similar to `Error: Error pinging Docker server: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?`, make sure that Docker is installed and running on your system.

Congratulations, you have provisioned a Docker container. 

## Destroy Infrastructure

Once you are done with this tutorial, remember to destroy this resource with the `destroy` command. The command asks for confirmation at the bottom of the output. Type `yes` and hit ENTER.

The `destroy` command asks for confirmation at the bottom of the output. Type `yes` and hit ENTER. Terraform destroys the resources it had created earlier.

```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_container.nginx: Refreshing state... [id=5bd271839b6581c0d271df0be9123ca7cc0129e41ed3f0a00268c0a4d841fb03]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the
last "terraform apply":

  # docker_container.nginx has been changed
  ~ resource "docker_container" "nginx" {
      + dns               = []
      + dns_opts          = []
      + dns_search        = []
      + group_add         = []
        id                = "5bd271839b6581c0d271df0be9123ca7cc0129e41ed3f0a00268c0a4d841fb03"
      + links             = []
      + log_opts          = {}
        name              = "training"
      + storage_opts      = {}
      + sysctls           = {}
      + tmpfs             = {}
        # (31 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }

Unless you have made equivalent changes to your configuration, or ignored the
relevant attributes using ignore_changes, the following plan may include
actions to undo or respond to these changes.

───────────────────────────────────────────────────────────────────────────────

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "5bd271839b65" -> null
      - id                = "5bd271839b6581c0d271df0be9123ca7cc0129e41ed3f0a00268c0a4d841fb03" -> null
      - image             = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5a" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest" -> null
      - latest      = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5a" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=5bd271839b6581c0d271df0be9123ca7cc0129e41ed3f0a00268c0a4d841fb03]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

The `destroy` command takes a while to run and displays a message indicating the resources were destroyed.

## Next steps

In this guide you have learned the basics of Terraform and how to manage a Docker container with configuration files.

For more details on the topics and products in this guide:

- Official Terraform [documentation](https://www.terraform.io/docs)
- The Terraform Docker provider [documentation](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs)
- Official Docker [documentation](https://docs.docker.com/)
- Official nginx [documentation](https://docs.nginx.com/)