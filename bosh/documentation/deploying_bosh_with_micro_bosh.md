# Deploy BOSH as an application using micro BOSH. #

1. Deploy micro BOSH. See the steps in the previous section.

1. Once your micro BOSH instance is deployed, you can target its Director:

		$ bosh micro status
		...
		Target         http://192.168.9.20:25555

		$ bosh target 192.168.9.20
		Target set to `micro-bosh'

		$ bosh status
		Director
                  Name      micro-bosh
                  URL       http://192.168.9.20:25555
                  Version   0.5.2 (release:ffed4d4a bosh:21e0b0bc)
                  User      admin
                  UID      e9b5d31d-1266-49d6-b25f-21df616e4017
                  CPI       vsphere
             
                Deployment
                  not set



### Download a BOSH stemcell

1. List public stemcells with bosh public stemcells

		% mkdir -p ~/stemcells
		% cd stemcells
		% bosh public stemcells
		+---------------------------------------+--------------------------------------------------+
                | Name                                  | Tags                                             |
                +---------------------------------------+--------------------------------------------------+
                | bosh-stemcell-aws-0.6.4.tgz           | aws, stable                                      |
                | bosh-stemcell-vsphere-0.6.4.tgz       | vsphere, stable                                  |
                | bosh-stemcell-vsphere-0.6.7.tgz       | vsphere, stable                                  | 
                | micro-bosh-stemcell-aws-0.6.4.tgz     | aws, micro, stable                               |
                | micro-bosh-stemcell-vsphere-0.6.4.tgz | vsphere, micro, stable                           |
                +---------------------------------------+--------------------------------------------------+
To download use 'bosh download public stemcell <stemcell_name>'.
		To download use 'bosh download public stemcell <stemcell_name>'.


1. Download a public stemcell. *NOTE, in this case you do not use the micro bosh stemcell.*

		bosh download public stemcell bosh-stemcell-vsphere-0.6.7.tgz

1. Upload the downloaded stemcell to micro BOSH.Before uploading you have to login to Micro BOSH.Default login/password is admin/admin.

		bosh upload stemcell bosh-stemcell-0.1.0.tgz

### Upload a BOSH release ###

1. You can create a BOSH release or use one of the public releases. The following steps show the use of a public release.

		cd /home/bosh_user
		gerrit clone ssh://[<your username>@]reviews.cloudfoundry.org:29418/bosh-release.git

1. Upload a public release from bosh-release

		cd /home/bosh_user/bosh-release/
		bosh upload release releases/bosh-10.yml


### Setup a BOSH deployment manifest and deploy ###

1. Create and setup a BOSH deployment manifest. Look at the sample BOSH manifest in (https://github.com/cloudfoundry/oss-docs/bosh/samples/bosh.yml). Assuming you have created a `bosh.yml` in `/home/bosh_user`.

		cd /home/bosh_user
		bosh deployment ./bosh.yml

1. Deploy BOSH

		bosh deploy

1. Target the newly deployed bosh director. In the sample `bosh.yml`, the bosh director has the ip address 192.0.2.36. So if you target this director with `bosh target http://192.0.2.36:25555` where 25555 is the default BOSH director port.  Your newly installed BOSH instance is now ready for use.
