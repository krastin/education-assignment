# Getting Started with Terraform

Terraform is a tool for defining and provisioning Infrastructure as Code (IaC).

To install Terraform, download the appropriate package for your system from [Terraform.io](https://www.terraform.io/downloads.html).

With Terraform installed, you are ready to create some infrastructure.

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
```

With Terraform initialized successfully, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The `apply` command takes a few minutes to run and displays a message indicating the resource was created.

Congratulations, you have provisioned a Docker container. Once you are done with this tutorial, remember to destroy this resource with the `destroy` command.

```shell
$ terraform destroy
```

The `destroy` command asks for confirmation at the bottom of the output. Type `yes` and hit ENTER. Terraform destroys the resources it had created earlier.