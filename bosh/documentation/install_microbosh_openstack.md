#Installing Micro bosh on a VM

##<b>Prerequisites:-</b>

 <ol>
   <li>Install the OpenStack. Please 
<a href="https://github.com/rajdeepd/bosh-oss-docs/tree/master/bosh/documentation/install_openstack.md">Click here</a> to know how to install.</li>
</ol>

Create a VM using horizon and you should be able to SSH into that VM- this is also called inception VM.


##Step 1: Create Inception VM

1. Login to dashboard as admin.
2. Select Project tab - > admin project.
3. Click on Access & Security - > Click on Create Keypair button.
4. Enter keypair name as “admin-keypair” and click on Create Keypair.
5. Save the keypair to some location like: /home/<username>/openstack/admin-keypair.pem
6. Copy the admin-keypair.pem to Inception VM.

     scp -i /root/.ssh/admin-keypair.pem  /root/.ssh/admin-keypair.pem ubuntu@192.168.22.34:/home/ubuntu

<b>Note:-</b> Remember the keypair location. we would use this pair many times later.

##Step 2: Login to vm

    $ sudo su
    (Enter password and hit Enter)

Check whether SSH Installed or not

    $ /etc/init.d/ssh status

If not installed install SSH

    $ apt-get install ssh

Create SSH Keys

    # ssh-keygen -t rsa

Output

    Generating public/private rsa key pair.
    # Enter file in which to save the key (/home/you/.ssh/id_rsa): <Click Enter>
    # Enter passphrase (empty for no passphrase):  <Click Enter>
    # Enter same passphrase again: <Click Enter>
    Your identification has been saved in /home/you/.ssh/id_rsa.
    Your public key has been saved in /home/you/.ssh/id_rsa.pub.
    The key fingerprint is:
    01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db

Copy the admin-keypair to /root/.ssh

    # cp /home/<username>/openstack/admin-keypair.pem /root/.ssh/.

Change permissions

    # chmod -R 600 /root/.ssh/.

Login to vm

    # ssh -i /root/.ssh/admin-keypair.pem ubuntu@192.168.22.34

    root@inception-vm:/home/ubuntu/#

<b>Note:-</b> 192.168.22.34 is the Inception VM ip address.


##Step 3 : Install Ruby

    root@inception-vm:/home/ubuntu/# sudo su

Install some core packages on Ubuntu that the BOSH deployer depends on.

    root@inception-vm:/home/ubuntu/# apt-get -y install build-essential libsqlite3-dev curl rsync git-core libmysqlclient-dev libxml2-dev libxslt-dev libpq-dev genisoimage

    root@inception-vm:/home/ubuntu/# \curl -L https://get.rvm.io | sudo bash -s stable

    root@inception-vm:/home/ubuntu/# source /etc/profile.d/rvm.sh

    root@inception-vm:/home/ubuntu/# rvm install 1.9.2-p280

    root@inception-vm:/home/ubuntu/# rvm use 1.9.2

    root@inception-vm:/home/ubuntu/# apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion pkg-config

Test RVM and Ruby

    root@inception-vm:/home/ubuntu/# rvm -v
    root@inception-vm:/home/ubuntu/# ruby -v

    Output:- It should show Ruby 1.9.2.

##Step 4 : Install BOSH CLI 

    root@inception-vm:/home/ubuntu/# gem install bosh_deployer --no-ri --no-rdoc

##Step 5 : Create Custom Micro Bosh Stemcell

Install the below dependencies before you run the below commands.
    root@inception-vm:/home/ubuntu/# apt-get install libpq-dev debootstrap kpartx qemu -y

Download the BOSH release and build it

    root@inception-vm:/home/ubuntu/# mkdir -p releases
    root@inception-vm:/home/ubuntu/# cd  releases
    root@inception-vm:/home/ubuntu/releases/# git clone git://github.com/frodenas/bosh-release.git
    root@inception-vm:/home/ubuntu/releases/# cd bosh-release
    root@inception-vm:/home/ubuntu/releases/bosh-release/# git submodule update --init


<b>Build the BOSH release</b>


    root@inception-vm:/home/ubuntu/releases/bosh-release/# bosh create release --with-tarball

If this is the first time you run bosh create release in the release repo, it will ask you to name the release, e.g. "bosh".

    Output will be like this:
    Release version: x.y-dev
    Release manifest: /home/ubuntu/releases/bosh-release/dev_releases/bosh-x.y-dev.yml
    Release tarball (95.2M): /home/ubuntu/releases/bosh-release/dev_releases/bosh-x.y-dev.tgz

<b>Install BOSH Agent</b>

    root@inception-vm:/home/ubuntu/releases/# cd /home/ubuntu/
    root@inception-vm:/home/ubuntu/# git clone git://github.com/frodenas/bosh.git
    root@inception-vm:/home/ubuntu/# cd bosh/agent/
    root@inception-vm:/home/ubuntu/bosh/agent/# bundle install --without=development test

    root@inception-vm:/home/ubuntu/bosh/agent/# apt-get install libpq-dev

<b>Install openstack registry</b>

    root@inception-vm:/home/ubuntu/# cd /home/ubuntu/bosh/openstack_registry
    root@inception-vm:/home/ubuntu/bosh/openstack_registry/# bundle install --without=development test
    root@inception-vm:/home/ubuntu/bosh/openstack_registry/# bundle exec rake install

<b>Build Custom Stemcell</b>

    root@inception-vm:/home/ubuntu/bosh/openstack_registry/# cd /home/ubuntu/bosh/agent
    root@inception-vm:/home/ubuntu/bosh/agent/# rake stemcell2:micro["openstack",/home/ubuntu/releases/bosh-release/micro/openstack.yml,/home/ubuntu/releases/bosh-release/dev_releases/bosh-x.y-dev.tgz]

<b>Note:-</b> Replace x.y with actual bosh version numbers. For example: bosh-0.6-dev.tgz


<b>Output will be like this:</b>

    Generated stemcell: 
    /var/tmp/bosh/agent-x.y.z-nnnnn/work/work/micro-bosh-stemcell-openstack-x.y.z.tgz

<b>Copy the generated stemcell to a safe location</b>

    root@inception-vm:/home/ubuntu/bosh/agent/# cd /home/ubuntu/
    root@inception-vm:/home/ubuntu/# mkdir -p stemcells
    root@inception-vm:/home/ubuntu/# cd stemcells
    root@inception-vm:/home/ubuntu/stemcells/# cp /var/tmp/bosh/agent-x.y.z-nnnnn/work/work/micro-bosh-stemcell-openstack-x.y.z.tgz .


##Step 7 : Use Bosh CLI on Inception VM to deploy Micro Bosh stemcell to Glance  This creates the Micro Bosh VM and it shows up in Horizon


    root@inception-vm:/home/ubuntu/# mkdir -p deployments/microbosh-openstack
    root@inception-vm:/home/ubuntu/# cd deployments/microbosh-openstack

<b>Create Manifest File</b>

    root@inception-vm:/home/ubuntu/deployments/microbosh-openstack/# vim micro-bosh.yml

Copy the below content and paste it in <b>micro-bosh.yml</b>


    name: microbosh-openstack

    env:
     bosh:
        password: $6$u/dxDdk4Z4Q3$MRHBPQRsU83i18FRB6CdLX0KdZtT2ZZV7BLXLFwa5tyVZbWp72v2wp.ytmY3KyBZzmdkPgx9D3j3oHaDZxe6F.


     level: DEBUG

    network:
     name: default
     type: dynamic
     label: private
     ip: 192.168.22.34


    resources:
     persistent_disk: 4096
     cloud_properties:
        instance_type: m1.small

    cloud:
      plugin: openstack
      properties:
       openstack:
           auth_url: http://10.0.0.2:5000/v2.0/tokens
           username: admin
           api_key: f00bar
           tenant: admin
           default_key_name: admin-keypair
           default_security_groups: ["default"]
           private_key: /root/.ssh/admin-keypair.pem



<b>Note:-</b>

    1. Replace Only the red colored values with actual ones.
    2. Generate hashed password for f00bar
    3. Replace the password with hashed password.
 
----

    root@inception-vm:/home/ubuntu/deployments/microbosh-openstack/# cd ..
    root@inception-vm:/home/ubuntu/deployments/# bosh micro deployment microbosh-openstack

<b>Output will be:</b>

    $ WARNING! Your target has been changed to `http://microbosh-openstack:25555'!
    Deployment set to '/home/ubuntu/deployments/microbosh-openstack/micro_bosh.yml'


<b>Deploy the deployment using the custom stemcell image</b>

    root@inception-vm:/home/ubuntu/deployments/# bosh micro deploy /home/ubuntu/stemcells/micro-bosh-stemcell-openstack-x.y.z.tgz

<b>Output will be:</b>

    Deploying new micro BOSH instance `microbosh-openstack/micro_bosh.yml' to `http://microbosh-openstack:25555' (type 'yes' to continue): yes

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
    Version: 0.6.4


    Deploy Micro BOSH
      unpacking stemcell (00:00:43)
      uploading stemcell (00:32:25)
      creating VM from 5aa08232-e53b-4efe-abee-385a7afb9421 (00:04:38)
      waiting for the agent (00:02:19)
      create disk (00:00:15)
      mount disk (00:00:07)
      stopping agent services (00:00:01)
      applying micro BOSH spec (00:01:20)
      starting agent services (00:00:00)
      waiting for the director (00:02:21)
    Done             11/11 00:44:30
    WARNING! Your target has been changed to `http://192.168.22.34:25555'!
    Deployment set to '/home/ubuntu/deployments/microbosh-openstack/micro_bosh.yml'
    Deployed `microbosh-openstack/micro_bosh.yml' to `http://microbosh-openstack:25555', took 00:44:30 to complete


<b>Now Test Bosh by connecting</b>

    root@inception-vm:/home/ubuntu/deployments/#  bosh target http://192.168.22.34

<b>Output will be:</b>

    Target set to `microbosh-openstack (http://192.168.22.34:25555) Ver: 0.6 (release:ce0274ec bosh:0d9ac4d4)'
    Your username: admin
    Enter password: *****
    Logged in as `admin'

<b>Note:-</b> It will ask for the username and password, enter admin for both.
