##  Running ECK on Google Kubernetes Engine (GKE)

> 💡 Recommended if you're using Google Cloud Platform and want to try it on
> a realistic cluster.

1.  Install tools specified in the previous section (Docker, kubectl, skaffold)

1.  Create a Google Kubernetes Engine cluster and make sure `kubectl` is pointing
    to the cluster.

    ```sh
    gcloud services enable container.googleapis.com
    ```

    ```sh
    gcloud container clusters create huage-eck-demo --num-nodes=3 --zone=europe-west1-b \
    --machine-type "n1-standard-2" --network "projects/elastic-sa/global/networks/default" \
    --subnetwork "projects/elastic-sa/regions/europe-west1/subnetworks/default"  \
    --enable-ip-alias --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing 
    ```

    ```
    kubectl get nodes
    ```

1.  Enable Google Container Registry (GCR) on your GCP project and configure the
    `docker` CLI to authenticate to GCR:

    ```sh
    gcloud services enable containerregistry.googleapis.com
    ```

    ```sh
    gcloud auth configure-docker -q
    ```

1.  Apply the ECK operator

    ```sh
    kubectl apply -f eck-all-in-one.yaml
    ```

    Verify the status of ECK operator
    
    ```sh
    kubectl -n elastic-system logs -f statefulset.apps/elastic-operator
    ```

1.  Create Elasticsearch nodes

    ```sh
    kubectl apply -f es-nodes.yaml
    ```
    Verify elasticsearch status
    
    ```sh
    kubectl get service eckdemo-es-http
    ```
    
    Retrive elasticsearch secret
    
    ```sh
    echo $(kubectl get secret eckdemo-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode)
    ```
1. Create Kibana nodes

   ```sh
   kubectl apply -f kibana-nodes.yaml
   ```
   Verify Kibana status
   
   ```sh
   kubectl get service eckdemo-kb-http
   ```
   