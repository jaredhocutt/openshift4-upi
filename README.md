# OpenShift 4 UPI

A repository of artifacts to improve the experience of install OpenShift 4
using the User-Provisioned Infrastructure (UPI) method of deployment.

## AWS

The [OpenShift 4 documentation][1] for UPI provides a collection of
CloudFormation templates to deploy OpenShift 4 infrastructure components.
However, these CloudFormation templates do not exactly represent the setup that
you would get if you were to do use the Installer-Provision Infrastructure
(IPI) method of deployment. Namely, it names things differently and misses
quite a few tags that are critical to ensuring that you can take advantage of
all cloud integration features in OpenShift 4.

To improve that exprience and to ensure the infrastructure provided by default
matches as closely as possible to what you would get if you used the IPI method
of deployment, this repository includes modified versions of each of the
CloudFormation templates to fix those issues.

You can find the templates here:

| Template                 | Original                                                             | Modified                                                    |
| ------------------------ | -------------------------------------------------------------------- | ----------------------------------------------------------- |
| VPC                      | [original](playbooks/aws/cloudformation/vpc.original.yaml)           | [modified](playbooks/aws/cloudformation/vpc.yaml)           |
| Network / Load Balancing | [original](playbooks/aws/cloudformation/network.original.yaml)       | [modified](playbooks/aws/cloudformation/network.yaml)       |
| Security                 | [original](playbooks/aws/cloudformation/security.original.yaml)      | [modified](playbooks/aws/cloudformation/security.yaml)      |
| Bootstrap                | [original](playbooks/aws/cloudformation/bootstrap.original.yaml)     | [modified](playbooks/aws/cloudformation/bootstrap.yaml)     |
| Control Plane            | [original](playbooks/aws/cloudformation/control_plane.original.yaml) | [modified](playbooks/aws/cloudformation/control_plane.yaml) |
| Worker                   | [original](playbooks/aws/cloudformation/worker.original.yaml)        | [modified](playbooks/aws/cloudformation/worker.yaml)        |

Also included is a playbook that ties each of the CloudFormation templates
together by matching the outputs of CloudFormation stacks to parameters to
templates that run later in the process, including taking user input for
required parameters and some optional parameters.

Example variable file:

```yaml
---

###############################################################################
# Required Variables
###############################################################################

cluster_name: test
base_domain: example.com
infrastructure_name: test-fgmdv

hosted_zone_name: "{{ base_domain }}"
hosted_zone_id: Z05602532C4FRVJXEMAGM

rhcos_ami: ami-0f4ecf819275850dd

bootstrap_ignition_location: 's3://com-example-test-ignition/bootstrap.ign'
ignition_ca: 'data:text/plain;charset=utf-8;base64,LS0tLS1CRUdJ...'

###############################################################################
# Optional Variables
###############################################################################

vpc_cidr: 10.0.0.0/16
availability_zone_count: 3
subnet_bits: 12

allowed_bootstrap_ssh_cidr: 0.0.0.0/0

auto_register_elb: "yes"

master_instance_type: m5.xlarge
```

Execute the playbook:

```bash
ansible-playbook -e @vars/aws.yml playbooks/aws/playbook.yml -v
```


[1]: https://docs.openshift.com/container-platform/latest/installing/installing_aws/installing-aws-user-infra.html
