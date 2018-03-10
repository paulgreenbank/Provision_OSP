# Ansible Playbook: Provision OSP

Provisions OpenStack RedHat Automation specialist 3Tier Application environment.

## Requirements

No special requirements are need for playbook and roles.

## Role Variables 

Available variables are listed below, along with set values (see `roles/openstack_instance/vars/{application[1,2].yml,database.yml,frontend.yml`):

    instance_name: {app1,app2,appdb1,frontend}

The name of the OpenStack instance being deployed. 

    group: {apps,appsdb,frontends}

Metadata added to instance at creation time, this is used to create in-memory inventory groups.

    deployment: dev

Metadata added to instance at creation time, identifying deployment type.

    instance_flavor: m2.small

Nova instance flavor defining the instance compute resources, this is created by the openstack_instance role.

    security_group: {application_servers,database_servers,frontend_servers}

Used to assign the correct security group to the instance, the allows only specific ports to be enabled for each instance type.

## Dependencies

  - None

## Example Playbook

The required playbooks have been created to call the roles to install the various instances required by the 3 Tier Application, the creation playbook is included here for reference:

	---
	## RedHat Ansible Boot Camp homework assignment
	## Playbook have been written as a PoC for a 3Tier Application deployment
	## All playbook have been written via Ansible Core and used in Ansible Tower

	## Created a written by Paul Greenbank - Technical Consultant for Open System Specialists

	## Playbook description:
	#
	# Playbook is run from the jumpbox inventory group which contains a server with access to the OpenStack
	# environment. The components are then deployed to configure a new OpenStack cloud to deploy the instances
	# and ultimately the 3Tier Application into.
	# Once the OpenStack Cloud is deployed 3 tasks are called to deploy the required OpenStack instances, these
	# are contolled via 3 seperate variable files, one for each component of the application.
	#

	## Playbook enhancements post PoC:
	#
	# Variablization for the `openstack_instance` role to use `with_subelements` would reduce the duplication of calling
	# this role 3 seperate times, it would also allow for adding more components to the application without changing the
	# role code.
	# Tags should also be added so that specific components could be deployed without calling the entire instance build
	# playbook, this would be benefical if you were to add more `apps` servers should demand grow.
	#

	# Playbook to create Openstack instances, setup networking and security

	- hosts: jumpbox

	  roles:
		- openstack_network
		- openstack_keypair
		- openstack_security
		- openstack_flavor

	  tasks:
	# Create Openstack database instance

	  - name: Create 3Tier application database instance
		include_role:
		  name: openstack_instance
		  vars_from: database.yml

	# Create Openstack application1 instance

	  - name: Create 3Tier application application1 instance
		include_role:
		  name: openstack_instance
		  vars_from: application1.yml

	# Create Openstack application2 instance

	  - name: Create 3Tier application application2 instance
		include_role:
		  name: openstack_instance
		  vars_from: application2.yml

	# Create Openstack frontend instances

	  - name: Create 3Tier application frontend instance
		include_role:
		  name: openstack_instance
		  vars_from: frontend.yml



## License

MIT / BSD

## Author Information

This role was created in 2018 by Paul Greenbank - Technical Consultant (https://www.oss.co.nz/).
