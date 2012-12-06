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

   Output of the above command is partially listed below:

           $bosh delete deployment cloudfoundry

           You are going to delete deployment `cloudfoundry'.

           THIS IS A VERY DESTRUCTIVE OPERATION AND IT CANNOT BE UNDONE!

           Are you sure? (type 'yes' to continue): yes

           Director task 42

           Deleting instances

4.Once cloudfoundry is deleted, nest step is to delete the appcloud release.You can get the appcloud release name as follows:

+ `bosh realeases`

   Output of the above command is similar to listing below:

          $ bosh releases

            +----------+-----------+
            | Name     | Versions  |
            +----------+-----------+
            | appcloud | 106, 119* |
            +----------+-----------+
            (*) Currently deployed

            Releases total: 1

5.Execute the following command to delete appcloud release:   

+ `bosh delete release appcloud`

   Output of the above command is partially listed below:

          $ bosh delete release appcloud
            Deleting `appcloud'
            Are you sure? (type 'yes' to continue): yes

            Director task 43

            Deleting packages

6.So Cloud Foundry has been deleted, we can now delete BOSH Deployment.First we have to delete the BOSH stemcell as follows:

+`bosh delete stemcell bosh-stemcell 0.6.4` # you can find stemcell name in the output of `bosh deployment` which we executed above

   Output of the above command is partially listed below:

          $ bosh delete stemcell bosh-stemcell 0.6.4
            Checking if stemcell exists...
            You are going to delete stemcell `bosh-stemcell/0.6.4'
            Are you sure? (type 'yes' to continue): yes

            Director task 44

            Deleting stemcell from cloud 

7.Once stemcell is deleted,the deployment target should have been set to Micro BOSH.Now BOSH Deployment can be deleted.Execute following command to get the name of BOSH deployment:

+ `bosh deployments`
   
   Output of the above command is similar to listing below:

        $ bosh deployments

          +------+------------+---------------------+
          | Name | Release(s) | Stemcell(s)         |
          +------+------------+---------------------+
          | bosh | bosh/10    | bosh-stemcell/0.6.4 |
          +------+------------+---------------------+

          Deployments total: 1

8.Execute following command to delete the BOSH deployment:

+`bosh delete deployment bosh`

  Output of the above command is partially listed below:

        $bosh delete deployment bosh 

         You are going to delete deployment `bosh'.

         THIS IS A VERY DESTRUCTIVE OPERATION AND IT CANNOT BE UNDONE!

         Are you sure? (type 'yes' to continue): yes

         Director task 6

         Deleting instances

 
