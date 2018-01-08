# elastic-search
This guide provides information about installing and upgrading when you are using more than one Elastic Stack product. It specifies the recommended order of installation and the steps you need to take to prepare for a stack upgrade

To begin with let us see in detail about elastic search and the features they have. Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements. 

Kibana, is an open source analytics and visualization platform designed to work with Elasticsearch. You use Kibana to search, view, and interact with data stored in Elasticsearch indices. You can easily perform advanced data analysis and visualize your data in a variety of charts, tables, and maps

​In our case, we need filebeat and metricbeat plugins to be installed in our elastic cloud as we are getting a licence for the product. 

# Requirements
Initially, to work with ELK(Elastic search, Logstash and Kibana) we need access to kuberenete. Additionally You need to have a recent version of Java installed.

# Installation

In elastic cloud version, already we will have access to elasticsearch and kibana. Initially elasticcloud comes with 14 days trial period, to continue later on with the product get the license. To get started use the following steps,

    * From the Elastic Cloud Console, click Create Cluster.
    * Choose the cluster size which suits the memory and storage requirement of your own.
    * Pick a cloud platform (AWS or google cloud) and a region.
    * set high availability generally to two data centers for production environment
    * select a cluster name of your choice, and click Create.
    
 To check if your cloud is created successfully, 
    * In the Elastic Cloud Console, go to the overview page and click the Elasticsearch endpoint URL under Endpoints.
    * Enter the credentials if prompted
 
 If you find the following message, then your cluster is successfully created.
 
 > {
 > "name" : "instance-0000000001",
 > "cluster_name" : "08bc0101dd9d0218a29c0c8feedbfa3c",
 > "cluster_uuid" : "B1Xs11KzRiujke8RTPT2cA",
 > "version" : {
 >   "number" : "6.1.1",
 >   "build_hash" : "bd92e7f",
 >   "build_date" : "2017-12-17T20:23:25.338Z",
 >   "build_snapshot" : false,
 >   "lucene_version" : "7.1.0",
 >   "minimum_wire_compatibility_version" : "5.6.0",
 >   "minimum_index_compatibility_version" : "5.0.0"
 > },
 > "tagline" : "You Know, for Search"
 > }
 
 
 
 
    
  
