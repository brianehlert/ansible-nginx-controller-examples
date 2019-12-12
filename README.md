NGINX Controller Example
=========

The whats
------------

note: all playbooks tested with Ubuntu 18.04 minimum AMI image

demo-vpc.json | Cloud Formation template to crete a VPC with 2 public and 2 private subnets.
controller_aws_vars.yaml | Update these vars for your VPC subnet placement and prepend names.
controller_environment_deploy_aws.yaml | Deploy machines into AWS for a Controller environment and external DB server.
nginx_controller_postgresql_install.yaml | install PostgreSQL and assumptions for Controller
nginx_installer_controller_vars.yaml | your Controller settings
nginx_install_controller.yaml | installs nginx controller and pre-requisites
nginx_controller_agent.yaml | installs the nginx Contorller agent on an nginx plus instance (see my nginx samples)