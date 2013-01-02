# Modify Database Size#

This document explains how to modify database size.In this example, we will modify mysql database size.


+ To check the current size of mysql database, login to mysql node. ou can get the IP of the cloud controller by executing following 
  command:

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

+ Login to any of the mysql node
  `ssh vcap@192.168.9.51`

+ Go to /var/vcap/jobs/cloud_controller/config

  `cd /var/vcap/jobs/mysql_node/config`

+ Read the cloudfoundry.yml for current DB size
  
  `cat mysql_node.yml`

        capacity: 200
        plan: free
        local_db: sqlite3:/var/vcap/store/mysql_node.db
        base_dir: /var/vcap/store/mysql
        mbus: nats://nats:aaa3ij3122@192.168.9.31:4222
        index: 0
        logging:
           level: debug
           file: /var/vcap/sys/log/mysql_node/mysql_node.log
  
           syslog: vcap.mysql_node
  
        pid: /var/vcap/sys/run/mysql_node/mysql_node.pid
        node_id: mysql_node_1
        supported_versions: ['5.1']
        default_version: '5.1'

        max_db_size: 256


+ To modify the database size, you need to update cloudfoundry deployment manifest file and then redeploy cloudfoundry.

+ Open cloudfoundry.yml in editing mode

  `vi cloudfoundry.yml`

  
+ Search for service_plans in the manifest file.Under service_plans  section ,all different databses with their configuration would be listed as follows:
       
         service_plans:
         mysql:
           free:
             job_management:
               high_water: 1400
               low_water: 100
             configuration:
               allow_over_provisioning: true
               capacity: 200
               max_db_size: 256
               max_long_query: 3
               max_long_tx: 0
               max_clients: 20
         postgresql:
           free:
             job_management:
               high_water: 1400
               low_water: 100
             configuration:
               capacity: 200
               max_db_size: 128
               max_long_query: 3
               max_long_tx: 30
               max_clients: 20
         mongodb:
           free:
             job_management:
               high_water: 3000
               low_water: 100
             

  

   
+ Lets increase max_db_size for mysql to 512

         service_plans:
         mysql:
           free:
             job_management:
               high_water: 1400
               low_water: 100
             configuration:
               allow_over_provisioning: true
               capacity: 200
               max_db_size: 512
               max_long_query: 3
               max_long_tx: 0
               max_clients: 20


+ If you placed your deployment manifest yml in ~/deployments/dev124, run the following command:

        `bosh deployment ~/deployments/dev124/cloudfoundry.yml`

        `Deployment set to '/home/rajdeep/deployments/cloudfoundry_new.yml'`

         
+ Redeploy cloudfoundry

         `bosh deploy`

Output of the above command would be similar to listing below:

          $bosh deploy
          Getting deployment properties from director...
          Compiling deployment manifest...
          Detecting changes in deployment...
          
          Release
          No changes
          
          Releases
          No changes
          
          Compilation
          No changes
          
          Update
          No changes
          
          Resource pools
          No changes
          
          Networks
          No changes
          
          Jobs
          No changes
          
          Properties
          service_plans
            mysql
              free
                configuration
                  changed max_db_size: 
                    - 256
                    + 512
          
          Please review all changes carefully
          Deploying `cloudfoundry.yml' to `dev124' (type 'yes' to continue): yes

+ Login to mysql node and check the DB size again


        $ cat mysql_node.yml 
        ---
        
        capacity: 200
        plan: free
        local_db: sqlite3:/var/vcap/store/mysql_node.db
        base_dir: /var/vcap/store/mysql
        mbus: nats://nats:aaa3ij3122@192.168.9.31:4222
        index: 0
        logging:
          level: debug
          file: /var/vcap/sys/log/mysql_node/mysql_node.log
          
          syslog: vcap.mysql_node
          
        pid: /var/vcap/sys/run/mysql_node/mysql_node.pid
        node_id: mysql_node_1
        supported_versions: ['5.1']
        default_version: '5.1'
        
        max_db_size: 512

Now max_db_size is 512

Using above procedure you can increase DB size of other databases e.g. postgresql etc


