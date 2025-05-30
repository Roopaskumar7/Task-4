
[Docker provider](https://www.terraform.io/docs/providers/docker/index.html).
You can get set up in a few simple steps, like so:

## 1. Install Docker

[Install Docker for Mac](https://docs.docker.com/docker-for-mac/install/) if
you have not already.

## 2. Install Terraform

Grab the latest Terraform binary for Darwin/macOS from
[releases.hashicorp.com](https://releases.hashicorp.com/terraform/)
and place the `terraform` binary somewhere in your `PATH`.

You can install also install Terraform with Homebrew like this:

```
brew install terraform
```

## 3. Configure, Initialize, Plan & Apply!

Start with a basic NGINX Docker container definition in a minimal Terraform
configuration — create a `main.tf` file with this command:

```
cat > main.tf << EOF
# Configure Docker provider and connect to the local Docker socket
provider "docker" {
  host = "unix:///var/run/docker.sock"
}

# Create NGINX container
resource "docker_container" "nginx" {
  image = "\${docker_image.nginx.latest}"
  name  = "enginecks"
  ports {
    internal = 80
    external = 80
  }
}

resource "docker_image" "nginx" {
  name = "nginx:latest"
}
EOF
```

### Initialize Terraform:

```
terraform init
```
### Generate Plan

Now, if the output from `terraform init` was without error, generate the plan:

```
terraform plan
```

### Apply Plan

Finally, if the plan is all good and without error, apply it:

```
terraform apply -auto-approve
```

The apply should complete without error and if so, you can move on to verifying the container status.

### Verify Container Status

Verify that the container is running:

```
docker ps -f name=enginecks --format "table {{.Names}}\t{{.Status}}"
```

The output should have something like this:

```
NAMES               STATUS
enginecks           Up 17 seconds
```


Now visit http://localhost in your browser and you should observe the default **Welcome to nginx!** default page.
