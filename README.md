# elastic-search
This guide provides information about installing and upgrading when you are using more than one Elastic Stack product. It specifies the recommended order of installation and the steps you need to take to prepare for a stack upgrade

To begin with let us see in detail about elastic search and the features they have. Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements. 

Kibana, is an open source analytics and visualization platform designed to work with Elasticsearch. You use Kibana to search, view, and interact with data stored in Elasticsearch indices. You can easily perform advanced data analysis and visualize your data in a variety of charts, tables, and maps

In our case, we need filebeat and metricbeat plugins to be installed in our elastic cloud as we are getting a licence for the product. 

## Requirements
Initially, to work with ELK(Elastic search, Logstash and Kibana) we need access to kuberenete. Additionally You need to have a recent version of Java installed.

## Installing elastic cloud

In elastic cloud version, already we will have access to elasticsearch and kibana. Initially elasticcloud comes with 14 days trial period, to continue later on with the product get the license. To get started use the following steps,

    * From the Elastic Cloud Console, click Create Cluster.
    * Choose the cluster size which suits the memory and storage requirement of your own.
    * Pick a cloud platform (AWS or google cloud) and a region.
    * set high availability generally to two data centers for production environment
    * select a cluster name of your choice, and click Create.
    
 To check if your cloud is created successfully, 
 
    1. In the Elastic Cloud Console, go to the overview page and click the Elasticsearch endpoint URL under Endpoints.
    2. Enter the credentials if prompted
 
 If you find the following message, then your cluster is successfully created.
 
 ` {
  "name" : "instance-0000000001",
  "cluster_name" : "08bc0101dd9d0218a29c0c8feedbfa3c",
  "cluster_uuid" : "B1Xs11KzRiujke8RTPT2cA",
  "version" : {
    "number" : "6.1.1",
    "build_hash" : "bd92e7f",
    "build_date" : "2017-12-17T20:23:25.338Z",
    "build_snapshot" : false,
    "lucene_version" : "7.1.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
 },
 "tagline" : "You Know, for Search"
 }`
 
   You can check the kibana dashboard being created in the overview page, click on the kibana icon in dashboard to view the visulaisations.
   
# Monitor kubernetes using metricbeat

## Metricbeats

Metricbeat is a lightweight shipper that you can install on your servers to periodically collect metrics from the operating system and from services running on the server. Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash. Here we are running metricbeats on kubernetes.

### Install metricbeats on cloud

To install metricbeats, use the following commands.

> curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.1.1-darwin-x86_64.tar.gz
> tar xzvf metricbeat-6.1.1-darwin-x86_64.tar.gz

Now open the metricbeat.yml file in the terminal and make the following changes,

   1. In kibana section enter the corresponding kibana endpoint configuration by specifing the path in 'setup.kibana.host:'
   2. In elastic cloud part, enter the cloud id in 'cloud.id:'
   3. In elasticsearch output specify the output to the cloud address and provide the authentication credentials and save the file.
   
Download the Metricbeat reference file from the following link using the curl command from your terminal

> curl -L -O https://raw.githubusercontent.com/elastic/beats/6.1/deploy/kubernetes/metricbeat-kubernetes.yaml

Once the file is downloaded, open the file using vi command in the terminal and make the required chnages metricbeat-kubernetes.yaml file. Enter your corresponding credentials provided when creating the cloud.

 `- name: ELASTICSEARCH_HOST
    value: //find the host address from the elastic cloud console
  - name: ELASTICSEARCH_PORT
    value: "9243"
  - name: ELASTICSEARCH_USERNAME
    value: elastic
  - name: ELASTICSEARCH_PASSWORD
   value: *****`

Once the file is edited, deploy the file using the following command.

> kubectl create -f metricbeat-kubernetes.yaml

### To check the status of the file

> kubectl --namespace=kube-system  get ds/metricbeat

### Start metricbeat

To start metricbeat use the following commands,
   1. sudo chown root metricbeat.yml 
   2. sudo chown root modules.d/system.yml 
   3. sudo ./metricbeat -e -c metricbeat.yml -d "publish"

### Creating index

Once the metricbeat plugin is deployed, it is essential to create an index. 

   1. Go to KIBANA dashboad, Mnagement -> Kibana -> Indexpatterns
   2. Select create index pattern
   3. Create index pattern name which suits the plugin such as metricbeat-*
   4. select next step and choose @time stamp option from the menue
   
 Indexing is successfully done. To view the logs choose the discover option from the menue

## Filebeat

Filebeat is a log data shipper for local files. Installed as an agent on your servers, Filebeat monitors the log directories or specific log files, tails the files, and forwards them either to Elasticsearch or Logstash for indexing. 

### Install Filebeat 

To install filebeats, use the following commands.

> curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.1.1-darwin-x86_64.tar.gz
> tar xzvf filebeat-6.1.1-darwin-x86_64.tar.gz
 
 ### Configure Filebeat
    
 Open filebeat.yml from your terminal and edit the following commands,
 
 1. Since you are sending output directly to Elasticsearch(cloud version and not using Logstash), set the IP address and port where Filebeat can find the Elasticsearch installation. 
      ` output.elasticsearch:
       hosts: ["#######"]`
 2. Configure the Kibana endpoint in 'setup.kibana:'
 
 ### Install default dashboards
 
 To install the predefined dashboards, use the following command
 
 > ./filebeat setup --dashboards 
 
 Note: Filebeat doesnot contains any default dashboards for kubernetes, It is essential to load the dashboards only if you are using filebeat to monitor other services such as NGINX, Kafka, MySQL
 
Download the Filebeat reference file from the following link using the curl command from your terminal

> curl -L -O https://raw.githubusercontent.com/elastic/beats/6.1/deploy/kubernetes/filebeat-kubernetes.yaml

Once the file is downloaded, open the file using vi command in the terminal and make the required chnages filebeat-kubernetes.yaml file. Enter your corresponding credentials provided when creating the cloud.

 `- name: ELASTICSEARCH_HOST
    value: //find the host address from the elastic cloud console
  - name: ELASTICSEARCH_PORT
    value: "9243"
  - name: ELASTICSEARCH_USERNAME
    value: elastic
  - name: ELASTICSEARCH_PASSWORD
   value: *****`
   
Once the file is edited, deploy the file using the following command.

> kubectl create -f metricbeat-kubernetes.yaml

Check teh status of the file by running the following command.

> kubectl --namespace=kube-system get ds/filebeat

### Start filebeat

To strart filebeat use the following commands,

   1. sudo chown root filebeat.yml 
   2. sudo ./filebeat -e -c filebeat.yml -d "publish"

