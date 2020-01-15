Hey guys
I have a short version for deploying wordpress and DB in GCP GKE

-To have it working you need the gcloud config set, mine is like below:
        PS C:\Users\Armando_Mayo\Documents\GitHub\WPProj> gcloud config list
        [compute]
        region = us-west1
        zone = us-west1-a
        [core]
        account = armandete@gmail.com
        disable_usage_reporting = True
        project = wp-proj1-264618
        Your active configuration is: [default]

-Also update your projectID in variables.tf as you see it when you select it from GCP
        variable "project" {
            default = "wp-proj1-264618"

My steps:
-    PS C:\Users\Armando_Mayo\Documents\GitHub\WPProj> terraform init
-    PS C:\Users\Armando_Mayo\Documents\GitHub\WPProj> terraform apply
        google_container_cluster.default: Refreshing state... [id=projects/wp-proj1-264618/locations/us-central1/clusters/armandete-cluster]
        google_container_node_pool.default: Refreshing state... [id=projects/wp-proj1-264618/locations/us-central1/clusters/armandete-cluster/nodePools/armandete-cluster-node-pool]
    -OUTPUTS
        google_container_cluster.default: Creation complete after 7m26s [id=projects/wp-proj1-264618/locations/us-central1/clusters/armandete-cluster]
        google_container_node_pool.default: Creation complete after 1m41s [id=projects/wp-proj1-264618/locations/us-central1/clusters/armandete-cluster/nodePools/armandete-cluster-node-pool]
        Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
        endpoint = 146.148.37.165
        master_version = 1.13.11-gke.14

-   After 10 minutes, cluster and pool is created, on clusters screen you can see it and click on "Connect", so you can redirect your kubectl commands to GCP, next is the command i got.
            gcloud container clusters get-credentials armandete-cluster --region us-central1 --project wp-proj1-264618
    -OUTPUTS
        PS C:\Users\Armando_Mayo\Documents\GitHub\WPProj> gcloud container clusters get-credentials armandete-cluster --region us-central1 --project wp-proj1-264618
        Fetching cluster endpoint and auth data.
        kubeconfig entry generated for armandete-cluster.
-   Then we proceed to install all .kub/ dir, includes wordpress, DB and Volume as described in files kub/*.yaml
    *This step takes few minutes as Load Balancer is created
    -OUTPUTS
        PS C:\Users\Armando_Mayo\Documents\GitHub\WPProj\kub> kubectl apply -k ./
        secret/mysql-pass-hh7cm974ch created
        service/wordpress-mysql created
        service/wordpress created
        deployment.apps/wordpress-mysql created
        deployment.apps/wordpress created
        persistentvolumeclaim/mysql-pv-claim created
        persistentvolumeclaim/wp-pv-claim created
-   At last from "Services & Ingress" screen we will see Load Balancer created and Endpoint
        wordpress - OK - Load balancer - 35.239.155.48:80
        http://35.239.155.48
