NGINX Controller Example
=========

The whats
------------

note: all playbooks tested with Ubuntu 18.04 minimum AMI image

`demo-vpc.json` | Cloud Formation template to crete a VPC with 2 public and 2 private subnets.

`controller_environment_deploy_aws.yaml` | Deploy machines into AWS for a Controller environment and external DB server, plus a load balancer and two workloads.
`controller_aws_vars.yaml` | Update these vars for your VPC subnet placement and prepend names.

`nginx_controller_postgresql_install.yaml` | install PostgreSQL and assumptions for Controller

`nginx_installer_controller_vars.yaml` | your Controller settings
`nginx_install_controller.yaml` | installs nginx controller and pre-requisites

`nginx_controller_agent_2.x.yaml` | installs the nginx Controller agent on an nginx plus instance (see [my nginx samples] (<https://github.com/brianehlert/ansible-nginx-examples)> )
`nginx_controller_agent_3.x.yaml` | same as the 2.x version but handles the difference in Controller authenticataion return

`nginx-plus-api.conf` and `stub_status.conf` | these fully enable all nginx metircs in Controller for the instance

`docker-daemon.json` | this sets the Docker daemon behavior accoring to Docker recommendations (cgroup driver) and sets up log rotation to prevent filling the partition, and the storage driver for Kubernetes.  So everyone is happy.
