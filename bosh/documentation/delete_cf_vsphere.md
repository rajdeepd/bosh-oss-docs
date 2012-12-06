# Deploy Cloud Foundry Deployment and BOSH #

This document helps you to delete Cloud Foundry Deployment and BOSH Deployment.

First, we will delete the Cloud Foundry deployment as follows:

1.Go to the cloudfoundry deployment directory.

+ `cd /home/user/cloudfoundry/deployments`

2. Execute command: `bosh deployments`
   Output of the above command is similar to listing below:

          $ bosh deployments

            +--------------+--------------+---------------------+
            | Name         | Release(s)   | Stemcell(s)         |
            +--------------+--------------+---------------------+
            | cloudfoundry | appcloud/119 | bosh-stemcell/0.6.4 |
            +--------------+--------------+---------------------+

3. Run the following command to delete the Cloud Foundry deployment:
  
+ `bosh delete deployment cloudfoundry`

   Output of the above command is similar to listing below:

           bosh delete deployment cloudfoundry

           You are going to delete deployment `cloudfoundry'.

           THIS IS A VERY DESTRUCTIVE OPERATION AND IT CANNOT BE UNDONE!

           Are you sure? (type 'yes' to continue): yes

           Director task 42

           Deleting instances


           
           