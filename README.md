NGINX Controller Example
===========================

Requirements
---------------

- Ansible 2.7+
- [Python boto3 library](https://aws.amazon.com/sdk-for-python/)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
- A default Access Key [configured in aws cli](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
- Python [python-pexpect](https://pexpect.readthedocs.io/en/stable/) package


The whats: Deploying Controller
---------------------------------

note: all playbooks tested with Ubuntu 18.04 minimum AMI image

`demo-vpc.json` | Cloud Formation template to create a VPC with 2 public and 2 private subnets.

`ansible-playbook controller_environment_deploy_aws.yaml`

- Deploys the machines to the vpc
- uses [controller_aws_vars.yaml](controller_aws_vars.yaml)
- writes individual inventory files in `playbook_dir`

`ansible-playbook nginx_controller_postgresql_install.yaml -i dbserver`

- installs PostgreSQL, naas user, naas database
- for some reason unclear to me at this time, this needs to be run a second time to overcome a failure

`ansible-playbook nginx_install_controller.yaml -i controller -e "su_password='<your controller installation password here>'"`

- installs Controller
- reads dbserver inventory file
- sets root password for su task (Ubuntu 18.04)
- requires [nginx_install_controller_vars.yaml](nginx_install_controller_vars.yaml)

`nginx_installer_controller_vars.yaml` | your Controller settings
`nginx_install_controller.yaml` | installs nginx controller and pre-requisites

`ansible-playbook nginx_controller_license.yaml -e "controller_fqdn=<your controller fqdn here>"`
- installs the Controller license
- requires a base64 file representing Controller license to be placed in `license/controller_license.base64.txt`

`ansible-playbook nginx_plus.yaml -i loadbalancers --private-key=<path to ssh key>`
- installs NGINX+ on the loadbalancers
- requires the NGINX+ repository certificate file to be placed at `/license/nginx-repo.key`
- requires the NGINX+ repository key file to be placed at `/license/nginx-repo.key`

`nginx_controller_agent_2x.yaml` | installs the nginx Controller agent on an nginx plus instance (see [my nginx samples] (<https://github.com/brianehlert/ansible-nginx-examples)> )
`ansible-playbook nginx_controller_agent_2x.yaml -i loadbalancers`

- for NGINX Controller 2.x family
- reads controller inventory file
- requires controller_user_email, controller_password

`nginx_controller_agent_3x.yaml` | same as the 2.x version but handles the difference in Controller authenticataion return
`ansible-playbook nginx_controller_agent_3x.yaml -i loadbalancers -e "controller_fqdn=<controller fqdn here>" --private-key=<path to ssh key>`

- for NGINX Controller 3.x family
- reads controller inventory file
- requires controller_user_email, controller_password


The whats part deaux: Configuring Controller
----------------------------------------------

- Create an environment in Controller (out of scope)

`ansible-playbook nginx_controller_certificate.yaml -e "@nginx_install_controller_vars.yaml" -e "controller_fqdn=<your controller fqdn here>"`

- Creates a Certificate to later attach to a gateway

`ansible-playbook nginx_controller_gateway.yaml -e "@nginx_install_controller_vars.yaml" -e "controller_fqdn=<your controller fqdn here>"`

- Creates a gateway


- Create an app (out of scope)

`ansible-playbook nginx_controller_component.yaml -e "@nginx_install_controller_vars.yaml" -e "controller_fqdn=<your controller fqdn here>"`

- Creates a component


Supplemental: What are these files for?
-----------------------------------------

`nginx-plus-api.conf` and `stub_status.conf` | these fully enable all nginx metrics in Controller for the instance

`docker-daemon.json` | this sets the Docker daemon behavior according to Docker recommendations (cgroup driver) and sets up log rotation to prevent filling the partition, and the storage driver for Kubernetes.  So everyone is happy.
