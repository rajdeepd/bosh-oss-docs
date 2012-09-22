# BOSH Installation #

Installation of BOSH is done using something called Micro BOSH, which is a single VM that includes all of the BOSH components in the same image. If you want to play around with BOSH, or create a simple development setup, you can install Micro BOSH using the [BOSH Deployer](#bosh-deployer). If you would like to use BOSH in production to manage a distributed system, you also use the BOSH Deployer, install Micro BOSH, and then use it as a means to deploy the final distributed system on multiple VMs.

A good way to think about this two step process is to consider that BOSH is a distributed system in itself. Since BOSH's core purpose is to deploy and manage distributed systems, it makes sense that we would use it to deploy itself. On the BOSH team, we gleefully refer to this as [Inception](http://en.wikipedia.org/wiki/Inception).

## BOSH bootstrap ##

### Prerequisites ###

1. We recommend that you run the BOSH bootstrap from Ubuntu since it is the distribution used by the BOSH team, and has been thoroughly tested.

1. Install some core packages on Ubuntu that the BOSH deployer depends on.

		sudo apt-get -y install libsqlite3-dev genisoimage

1. Ruby 1.9.2 or later.

1. Install the BOSH Deployer ruby gem.

		gem install bosh_deployer

Once you have installed the deployer, you will see some extra commands appear after typing `bosh` on your command line.

**The `bosh micro` commands must be run within a micro BOSH deployment directory**

		% bosh help
		...
		Micro
			micro deployment [<name>] Choose micro deployment to work with
			micro status              Display micro BOSH deployment status
			micro deployments         Show the list of deployments
			micro deploy <stemcell>   Deploy a micro BOSH instance to the currently
                                      selected deployment
                            --update  update existing instance
			micro delete              Delete micro BOSH instance (including
                                      persistent disk)
			micro agent <args>        Send agent messages
			micro apply <spec>        Apply spec


### Configuration ###

For a minimal vSphere configuration example, see: `https://github.com/cloudfoundry/bosh/blob/master/deployer/spec/assets/test-bootstrap-config.yml`. Note that `disk_path` is `BOSH_Deployer` rather than `BOSH_Disks`. A datastore folder other than `BOSH_Disks` is required if your vCenter hosts other Directors. The `disk_path` folder needs to be created manually. Also, your configuration must live inside a `deployments` directory and follow the convention of having a `$name` subdir containing `micro_bosh.yml`, where `$name` is your deployment name.

For example:

		% find deployments -name micro_bosh.yml
		deployments/vcs01/micro_bosh.yml
		deployments/dev32/micro_bosh.yml
		deployments/dev33/micro_bosh.yml

Deployment state is persisted to `deployments/bosh-deployments.yml`.

### vCenter Configuration ###

The Virtual Center configuration section looks like the following.

		cloud:
		  plugin: vsphere
		  properties:
		    agent:
		      ntp:
		        - <ntp_host_1>
		        - <ntp_host_2>
		     vcenters:
		       - host: <vcenter_ip>
		         user: <vcenter_userid>
		         password: <vcenter_password>
		         datacenters:
		           - name: <datacenter_name>
		             vm_folder: <vm_folder_name>
		             template_folder: <template_folder_name>
		             disk_path: <subdir_to_store_disks>
		             datastore_pattern: <data_store_pattern>
		             persistent_datastore_pattern: <persistent_datastore_pattern>
		             allow_mixed_datastores: <true_if_persistent_datastores_and_datastore_patterns_are_the_same>
		             clusters:
		             - <cluster_name>:
		                 resource_pool: <resource_pool_name>

If you want to create a role for the bosh user in vCenter, these are the privileges needed:

| Object 	| Permission 	|
| -----------	| ------------------------------	|
| Datastore 	| 	|
| 	| allocate space 	|
| 	| browse datastore 	|
| 	| low-level file operations 	|
| 	| remove file 	|
| 	| update virtual machine files 	|
| Folder (ALL) 	| 	|
| 	| create folder 	|
| 	| delete folder 	|
| 	| move folder 	|
| 	| rename folder 	|
| Global 	| 	|
| 	| cancel task 	|
| 	| diagnostics 	|
| Host / Configuration (All) 	| 	|
| 	| advanced setting 	|
| 	| authentication store 	|
| 	| change date & time settings 	|
| 	| change PCIPassthru settings 	|
| 	| change SNMP settings 	|
| 	| connection 	|
| 	| firmware 	|
| 	| hyperthreading 	|
| 	| maintenance 	|
| 	| memory configuration 	|
| 	| network configuration 	|
| 	| power 	|
| 	| query patch 	|
| 	| security profile and firewall 	|
| 	| storage partition configuration 	|
| 	| system management 	|
| 	| system resources 	|
| 	| virtual machine auto-start configuration 	|
| Host / Inventory (ALL) 	| 	|
| 	| add host to cluster 	|
| 	| add stand-alone host 	|
| 	| create cluster 	|
| 	| modify cluster 	|
| 	| move cluster or stand-alone host 	|
| 	| move host 	|
| 	| remove cluster 	|
| 	| remove host 	|
| 	| rename cluster 	|
| Host / Local Operations 	| 	|
| 	| create virtual machine 	|
| 	| delete virtual machine 	|
| 	| reconfigure vitrual machine 	|
| Network 	| 	|
| 	| assign network 	|
| Resource (ALL) 	| 	|
| 	| apply recommendation 	|
| 	| assign vApp to resource pool 	|
| 	| assign vitrual machine to resource pool 	|
| 	| create resource pool 	|
| 	| migrate 	|
| 	| modify resource pool 	|
| 	| move resource pool 	|
| 	| query vmotion 	|
| 	| relocate 	|
| 	| remove resource pool 	|
| 	| rename resouce pool 	|
|           Scheduled Task (ALL) 	|
| 	| create tasks 	|
| 	| modify tasks 	|
| 	| remove tasks 	|
| 	| run task 	|
|           Sessions 	| 	|
| 	| view and stop sessions 	|
|           Tasks (ALL) 	| 	|
| 	| create tasks 	|
| 	| update tasks 	|
|           vApp (ALL) 	| 	|
| 	| add virtual machine 	|
| 	| assign resource pool 	|
| 	| assign vApp 	|
| 	| clone 	|
| 	| create 	|
| 	| delete 	|
| 	| export 	|
| 	| import 	|
| 	| move 	|
| 	| power off 	|
| 	| power on 	|
| 	| rename 	|
| 	| suspend 	|
| 	| unregister 	|
| 	| vApp application configuration 	|
| 	| vApp instance configruation 	|
| 	| vApp resouce configuration 	|
| 	| view OVF environment 	|
| Virtual Machine (ALL) / Configuration (All) 	| 	|
| 	| add existing disk 	|
| 	| add new disk 	|
| 	| add or remove device 	|
| 	| advanced 	|
| 	| change CPU count 	|
| 	| change resouce 	|
| 	| disk change tracking 	|
| 	| disk lease 	|
| 	| extend virtul disk 	|
| 	| host USB device 	|
| 	| memory 	|
| 	| modify device settings 	|
| 	| query fault tolerance compatability 	|
| 	| query unowned files 	|
| 	| raw device 	|
| 	| reload from path 	|
| 	| remove disk 	|
| 	| rename 	|
| 	| reset guest information 	|
| 	| settings 	|
| 	| swap file placement 	|
| 	| unlock virtual machine 	|
| 	| upgrade virtual hardware 	|
| Virtual Machine (ALL) / Interaction (ALL) 	| 	|
| 	| acquire guest control ticket 	|
| 	| answer question 	|
| 	| backup operation on virtual machine 	|
| 	| configure CD media 	|
| 	| configure floppy media 	|
| 	| console interaction 	|
| 	| create screenshot 	|
| 	| defragment all disks 	|
| 	| device connection 	|
| 	| disable fault tolerance 	|
| 	| enable fault tolerance 	|
| 	| power off 	|
| 	| power on 	|
| 	| record session on virtual machine 	|
| 	| replay session on virtual machine 	|
| 	| reset 	|
| 	| suspend 	|
| 	| test failover 	|
| 	| test restart secondary VM 	|
| 	| turn off fault tolerance 	|
| 	| turn on fault tolerance 	|
| 	| VMware Tools install 	|
| Virtual Machine (ALL) / Inventory (ALL) 	| 	|
| 	| create from existing 	|
| 	| create new 	|
| 	| move 	|
| 	| register 	|
| 	| remove 	|
| 	| unregister 	|
| Virtual Machine (ALL) / Provisioning (ALL) 	| 	|
| 	| allow disk access 	|
| 	| allow read-only disk access 	|
| 	| allow virtual machine download 	|
| 	| allow virtual machine files upload 	|
| 	| clone template 	|
| 	| clone virtual machine 	|
| 	| create templace from virtual machine 	|
| 	| customize 	|
| 	| deploy template 	|
| 	| mark as template 	|
| 	| mark as virtual machine 	|
| 	| modify customization specification 	|
| 	| promote disks 	|
| 	| read customization specifications 	|
| Virtual Machine (ALL) / State (ALL) 	| 	|
| 	| create snapshot 	|
| 	| remove snapshot 	|
| 	| rename snapshot 	|
| 	| revert to snapshot 	|

Before you can run micro BOSH deployer, you have to do the following within Virtual Center:

1. Create the vm_folder

1. Create the template_folder

1. Create the disk_path in the appropriate datastores

1. Create the resource_pool.

Resource pool is optional you can run without a resource pool. Without a resource pool the cluster property looks like:

            		persistent_datastore_pattern: <datastore_pattern>
            		allow_mixed_datastores: <true_if_persistent_datastores_and_datastore_patterns_are_the_same>
            		clusters:
            		- <cluster_name>

The datastore pattern above could just be the name of a datastore or some regular expression matching the datastore name.

If you have a datastore called "vc_data_store_1" and you would like to use this datastore for both persistent and non persistent disks. Your config would look like:

            		datastore_pattern: vc_data_store_1
            		persistent_datastore_pattern:  vc_data_store_1
            		allow_mixed_datastores: true

If you have 2 datastores called "vc_data_store_1", "vc_data_store_2" and you would like to use both datastore for both persistent and non persistent disks. Your config would look like:

            		datastore_pattern: vc_data_store_?
            		persistent_datastore_pattern:  vc_data_store_?
            		allow_mixed_datastores: true

If you have 2 datastores called "vnx:1",  "vnx:2" and you would like to separate your persistent and non persistent disks. Your config would look like

            		datastore_pattern: vnx:1
            		persistent_datastore_pattern: vnx:2
            		allow_mixed_datastores: false

### Deployment ###

1. Download a micro BOSH Stemcell:

		% mkdir -p ~/stemcells
		% cd stemcells
		% bosh public stemcells
		+-------------------------------+----------------------------------------------------+
		| Name                          | Url                                                |
		+-------------------------------+----------------------------------------------------+
		| bosh-stemcell-0.4.7.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...h120= |
		| micro-bosh-stemcell-0.1.0.tgz | https://blob.cfblob.com/rest/objects/4e4e7...5Mms= |
		| bosh-stemcell-0.3.0.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...mw1w= |
		| bosh-stemcell-0.4.4.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...r144= |
		+-------------------------------+----------------------------------------------------+
		To download use 'bosh download public stemcell <stemcell_name>'.
		% bosh download public stemcell micro-bosh-stemcell-0.1.0.tgz


1. Set the micro BOSH Deployment using:

		% cd /var/vcap/deployments
		% bosh micro deployment dev33
		Deployment set to '/var/vcap/deployments/dev33/micro_bosh.yml'

1. Deploy a new micro BOSH instance and create a new persistent disk.

		% bosh micro deploy ~/stemcells/micro-bosh-stemcell-0.1.0.tgz

1. Update an existing micro BOSH instance. The existing persistent disk will be attached to the new VM.

		% bosh micro deploy ~/stemcells/micro-bosh-stemcell-0.1.1.tgz --update

### Deleting a micro BOSH deployment ###

The `delete` command will delete the VM, Stemcell, and persistent disk.

Example:

		% bosh micro delete

### Checking Status of a micro BOSH deploy ###

The status command will show the persisted state for a given micro BOSH instance.

		% bosh micro status
		Stemcell CID   sc-f2430bf9-666d-4034-9028-abf9040f0edf
		Stemcell name  micro-bosh-stemcell-0.1.0
		VM CID         vm-9cc859a4-2d51-43ca-8dd5-220425518fd8
		Disk CID       1
		Deployment     /var/vcap/deployments/dev33/micro_bosh.yml
		Target         micro (http://11.23.194.100:25555) Ver: 0.3.12 (00000000)

### Listing Deployments ###

The `deployments` command prints a table view of deployments/bosh-deployments.yml.

		% bosh micro deployments

The files in the `deployments` directory need to be saved if you later want to be able to update your micro BOSH instance. They are all text files, so you can commit them to a git repository to make sure they are safe in case your bootstrap VM goes away.

### Applying a specification

The micro-bosh-stemcell includes an embedded `apply_spec.yml`. This command can be used to apply a different spec to an existing instance. The `apply_spec.yml` properties are merged with your Deployment's network.ip and cloud.properties.vcenters properties.

		% bosh micro apply apply_spec.yml

### Sending messages to the micro BOSH agent ###

The CLI can send messages over HTTP to the agent using the `agent` command.

Example:

		% bosh micro agent ping
		"pong"


## Building your own micro BOSH stemcell for AWS

If you want to create your own micro BOSH stemcell to use with AWS, this section describes the steps needed to do it, however, the recommended way is to download one of the public stemcells.

### Build the BOSH release

First you need to create a tarball of the bosh release

		cd ~
		git clone git@github.com:cloudfoundry/bosh-release.git
		cd ~/bosh-release
		git submodule update --init
		bosh create release --with-tarball

If this is the first time you run `bosh create release` in the release repo, it will ask you to name the release, e.g. "`bosh`", and then the output will be `dev_releases/bosh-n.tgz`.

### Micro BOSH manifest

Now you need the micro BOSH manifest file, which is available in the `bosh-release` repo in `micro/aws.yml`. It describes the components of micro BOSH and how they should be configured.

		---
		deployment: micro
		release:
		  name: micro
		  version: 9
		configuration_hash: {}
		properties:
		  micro: true
		  domain: vcap.me
		  env:
		  networks:
		    apps: local
		    management: local
		  nats:
		    user: nats
		    password: nats
		    address: 127.0.0.1
		    port: 4222
		  redis:
		    address: 127.0.0.1
		    port: 25255
		    password: redis
		  postgres:
		    user: postgres
		    password: postgres
		    address: 127.0.0.1
		    port: 5432
		    database: bosh
		  blobstore:
		    address: 127.0.0.1
		    backend_port: 25251
		    port: 25250
		    director:
		      user: director
		      password: director
		    agent:
		      user: agent
		      password: agent
		  director:
		    address: 127.0.0.1
		    name: micro
		    port: 25555
		  aws_registry:
		    address: 127.0.0.1
		    http:
		      port: 25777
		      user: admin
		      password: admin
		    db:
		      database: postgres://postgres:postgres@localhost/bosh
		      max_connections: 32
		      pool_timeout: 10
		    aws:
		      max_retries: 2
		  hm:
		    http:
		      port: 25923
		      user: hm
		      password: hm
		    loglevel: info
		    director_account:
		      user: admin
		      password: admin
		    intervals:
		      poll_director: 60
		      poll_grace_period: 30
		      log_stats: 300
		      analyze_agents: 60
		      agent_timeout: 180
		      rogue_agent_alert: 180

**Note that this manifest has passwords that are publicly known, but they can and should be overridden during the deployment**

### Build micro BOSH stemcell

Now you have all the pieces to assemble the micro BOSH AWS stemcell

		cd ~/bosh/agent
		rake stemcell2:micro[aws,~/bosh-release/aws.yml,~/bosh-release/dev_builds/bosh-1.tgz]

This outputs the stemcell in `/var/tmp/bosh/agent-x.y.z-nnnnn/work/work/micro-bosh-stemcell-aws-x.y.z.tgz`

### Deploy it

Before you can deploy, you need to upload the stemcell you built to your AWS bootstrap VM if you built the stemcell on your local system:

		scp micro-bosh-stemcell-aws-x.y.z.tgz ubuntu@ec2-nnn-nnn-nnn-nnn.compute-1.amazonaws.com:

Then log on to the AWS VM and create the file `~/deployments/aws/micro_bosh.yml`

		---
		name: micro-bosh-aws

		logging:
		  level: DEBUG

		network:
		  type: dynamic

		resources:
		  cloud_properties:
		    instance_type: m1.small

		cloud:
		  plugin: aws
		  properties:
		    aws:
		      access_key_id: ...
		      secret_access_key: ...
		      default_key_name: ...
		      default_security_groups: ["default"]
		      ec2_private_key: ~/.ssh/ec2.pem

		apply_spec:
          properties:
            nats:
              user: ...
              password: ...
            postgres:
              user: ...
              password: ...
            ...

Finally run

		cd ~/deployments
		bosh micro deployment aws
		bosh micro deploy ~/micro-bosh-stemcell-aws-x.y.z.tgz

A successful deployment looks like this:

        $ bosh micro deploy ~/micro-bosh-stemcell-aws-x.y.z.tgz
        Deploying new micro BOSH instance `aws/micro_bosh.yml' to `micro-bosh-aws' (type 'yes' to continue): yes

        Verifying stemcell...
        File exists and readable                                     OK
        Manifest not found in cache, verifying tarball...
        Extract tarball                                              OK
        Manifest exists                                              OK
        Stemcell image file                                          OK
        Writing manifest to cache...
        Stemcell properties                                          OK

        Stemcell info
        -------------
        Name:    micro-bosh-stemcell
        Version: x.y.z


        Deploy Micro BOSH
          unpacking stemcell (00:00:12)
          uploading stemcell (00:06:20)
          creating VM from ami-a99d34c0 (00:00:24)
          waiting for the agent (00:02:29)
          create disk (00:00:00)
          mount disk (00:00:20)
          stopping agent services (00:00:01)
          applying micro BOSH spec (00:00:25)
          starting agent services (00:00:00)
          waiting for the director (00:01:21)
        Done             11/11 00:11:44
        WARNING! Your target has been changed to `http://nnn.nnn.nnn.nnn:25555'!
        Deployment set to '/home/ubuntu/deployments/aws/micro_bosh.yml'
        Deployed `aws/micro_bosh.yml' to `micro-bosh-aws', took 00:11:44 to complete

## Deploy BOSH as an application using micro BOSH. ##

1. Deploy micro BOSH. See the steps in the previous section.

1. Once your micro BOSH instance is deployed, you can target its Director:

		$ bosh micro status
		...
		Target         micro (http://11.23.194.100:25555) Ver: 0.3.12 (00000000)

		$ bosh target http://11.23.194.100:25555
		Target set to 'micro (http://11.23.194.100:25555) Ver: 0.3.12 (00000000)'

		$ bosh status
		Updating director data... done

		Target         micro (http://11.23.194.100:25555) Ver: 0.3.12 (00000000)
		UUID           b599c640-7351-4717-b23c-532bb35593f0
		User           admin
		Deployment     not set

### Download a BOSH stemcell

1. List public stemcells with bosh public stemcells

		% mkdir -p ~/stemcells
		% cd stemcells
		% bosh public stemcells
		+-------------------------------+----------------------------------------------------+
		| Name                          | Url                                                |
		+-------------------------------+----------------------------------------------------+
		| bosh-stemcell-0.4.7.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...h120= |
		| micro-bosh-stemcell-0.1.0.tgz | https://blob.cfblob.com/rest/objects/4e4e7...5Mms= |
		| bosh-stemcell-0.3.0.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...mw1w= |
		| bosh-stemcell-0.4.4.tgz       | https://blob.cfblob.com/rest/objects/4e4e7...r144= |
		+-------------------------------+----------------------------------------------------+
		To download use 'bosh download public stemcell <stemcell_name>'.


1. Download a public stemcell. *NOTE, in this case you do not use the micro bosh stemcell.*

		bosh download public stemcell bosh-stemcell-0.1.0.tgz

1. Upload the downloaded stemcell to micro BOSH.

		bosh upload stemcell bosh-stemcell-0.1.0.tgz

### Upload a BOSH release ###

1. You can create a BOSH release or use one of the public releases. The following steps show the use of a public release.

		cd /home/bosh_user
		gerrit clone ssh://[<your username>@]reviews.cloudfoundry.org:29418/bosh-release.git

1. Upload a public release from bosh-release

		cd /home/bosh_user/bosh-release/releases/
		bosh upload release bosh-1.yml


### Setup a BOSH deployment manifest and deploy ###

1. Create and setup a BOSH deployment manifest. Look at the sample BOSH manifest in (https://github.com/cloudfoundry/oss-docs/bosh/samples/bosh.yml). Assuming you have created a `bosh.yml` in `/home/bosh_user`.

		cd /home/bosh_user
		bosh deployment ./bosh.yml

1. Deploy BOSH

		bosh deploy.

1. Target the newly deployed bosh director. In the sample `bosh.yml`, the bosh director has the ip address 192.0.2.36. So if you target this director with `bosh target http://192.0.2.36:25555` where 25555 is the default BOSH director port.  Your newly installed BOSH instance is now ready for use.

# Deploying to AWS using BOSH

The BOSH cloud provider interface for AWS allows BOSH to deploy to AWS.

## AWS cloud properties

The cloud properties specific to AWS are

### Resource pools

1. `key_name`

1. `availability_zone`

1. `instance_type`

### Networks

1. `type`

1. `ip`

## Security concern deploying Cloud Foundry to AWS

If you deploy [Cloud Foundry](https://github.com/cloudfoundry/cf-release) to AWS using BOSH, the deployment property `nfs_server.network` needs to be set to `*` (or `10/8`) as we don't have a way to limit the list of IPs belonging to the deployment. To limit access, create and use a security group.

