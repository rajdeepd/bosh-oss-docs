# Modify User Account Capacity#

This document explains how to increase/decrease memory,apps and services limits for users.


+ Login to cloud_controller. You can get the IP of the cloud controller by executing following command:

  `bosh vms`

   Output of the above command is similar to listing below:


          $ bosh vms

            +-----------------------------+---------+----------------+---------------+
            | Job/index                   | State   | Resource Pool  | IPs           |
            +-----------------------------+---------+----------------+---------------+
            | acm/0                       | running | infrastructure | 192.168.9.38  |
            | acmdb/0                     | running | infrastructure | 192.168.9.37  |
            | backup_manager/0            | running | infrastructure | 192.168.9.120 |
            | ccdb_postgres/0             | running | infrastructure | 192.168.9.32  |
            | cloud_controller/0          | running | infrastructure | 192.168.9.213 |
            | cloud_controller/1          | running | infrastructure | 192.168.9.214 |
            | collector/0                 | running | infrastructure | 192.168.9.210 |
            | dashboard/0                 | running | infrastructure | 192.168.9.211 |
            | dea/0                       | running | deas           | 192.168.9.186 |
            | dea/1                       | running | deas           | 192.168.9.187 |
            | dea/2                       | running | deas           | 192.168.9.188 |
            | dea/3                       | running | deas           | 192.168.9.189 |
            | debian_nfs_server/0         | running | infrastructure | 192.168.9.30  |
            | hbase_master/0              | running | infrastructure | 192.168.9.44  |
            | hbase_slave/0               | running | infrastructure | 192.168.9.41  |
            | hbase_slave/1               | running | infrastructure | 192.168.9.42  |
            | hbase_slave/2               | running | infrastructure | 192.168.9.43  |
            | health_manager/0            | running | infrastructure | 192.168.9.163 |
            | login/0                     | running | infrastructure | 192.168.9.162 |
            | mongodb_gateway/0           | running | infrastructure | 192.168.9.222 |
            | mongodb_node/0              | running | infrastructure | 192.168.9.60  |
            | mongodb_node/1              | running | infrastructure | 192.168.9.61  |
            | mysql_gateway/0             | running | infrastructure | 192.168.9.221 |
            | mysql_node/0                | running | infrastructure | 192.168.9.51  |
            | mysql_node/1                | running | infrastructure | 192.168.9.52  |
            | nats/0                      | running | infrastructure | 192.168.9.31  |
            | opentsdb/0                  | running | infrastructure | 192.168.9.34  |
            | postgresql_gateway/0        | running | infrastructure | 192.168.9.192 |
            | postgresql_node/0           | running | infrastructure | 192.168.9.90  |
            | postgresql_node/1           | running | infrastructure | 192.168.9.91  |
            | rabbit_gateway/0            | running | infrastructure | 192.168.9.191 |
            | rabbit_node/0               | running | infrastructure | 192.168.9.80  |
            | rabbit_node/1               | running | infrastructure | 192.168.9.81  |
            | redis_gateway/0             | running | infrastructure | 192.168.9.190 |
            | redis_node/0                | running | infrastructure | 192.168.9.70  |
            | redis_node/1                | running | infrastructure | 192.168.9.71  |
            | router/0                    | running | infrastructure | 192.168.9.101 |
            | router/1                    | running | infrastructure | 192.168.9.102 |
            | serialization_data_server/0 | running | infrastructure | 192.168.9.123 |
            | service_utilities/0         | running | infrastructure | 192.168.9.121 |
            | services_nfs/0              | running | infrastructure | 192.168.9.50  |
            | services_redis/0            | running | infrastructure | 192.168.9.72  |
            | stager/0                    | running | infrastructure | 192.168.9.215 |
            | stager/1                    | running | infrastructure | 192.168.9.216 |
            | syslog_aggregator/0         | running | infrastructure | 192.168.9.33  |
            | uaa/0                       | running | infrastructure | 192.168.9.212 |
            | uaadb/0                     | running | infrastructure | 192.168.9.35  |
            | vblob_gateway/0             | running | infrastructure | 192.168.9.193 |
            | vblob_node/0                | running | infrastructure | 192.168.9.110 |
            | vcap_redis/0                | running | infrastructure | 192.168.9.36  |
            +-----------------------------+---------+----------------+---------------+


+ In the out put of above command , you can see two cloud controller instances. Changes need to be made in both.So login to cloud 
  controller

  `ssh vcap@192.168.9.223`

  
+ Go to /var/vcap/jobs/cloud_controller/config

  `cd /var/vcap/jobs/cloud_controller/config`

   
+ Open cloud_controller.yml file in editing mode. You need to use sudo rights.

 `sudo vi cloud_contrller.yml`


+ Change the normal user account capcity as per your requirement:

         # Normal users limited to 512M, 4 Services, and 4 URIs per App
           default_account_capacity:

             memory:  2048
             app_uris: 4
             services: 16
             apps:     20


+ For example, lets change apps to 30

           # Normal users limited to 512M, 4 Services, and 4 URIs per App
           default_account_capacity:

             memory:  2048
             app_uris: 4
             services: 16
             apps:     30

+ Restart the cloud_controller instance.

+ Make similar changes in other cloud controller instance and restart that job.

+ Login with any user except on vmc and execute `vmc info`.

         $vmc login
          Attempting login to [http://api.cf.com]
          Email: abc@gmail.com
          Password: *******
          Successfully logged into [http://api.cf.com]

         $vmc info

          VMware's Cloud Application Platform
          For support visit http://support.cloudfoundry.com

          Target:   http://api.cf.com (v0.999) 
          Client:   v0.3.18

          User:     abc@gmail.com
          Usage:    Memory   (256.0M of 2.0G total)
          Services (0 of 16 total)
          Apps     (2 of 30 total)

 Apps count has been increased to 30.

 Using the above procedure you can also increase total memory and services limit for a user.
