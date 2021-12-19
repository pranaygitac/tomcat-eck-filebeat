# tomcat-eck-filebeat
export running tomcat pod logs using filebeat to eck

#STEP1:Deploy ECK in your Kubernetes clusteredit

kubectl create -f https://download.elastic.co/downloads/eck/1.9.1/crds.yaml
kubectl apply -f https://download.elastic.co/downloads/eck/1.9.1/operator.yaml

                         OR
 https://www.elastic.co/guide/en/cloud-on-k8s/master/k8s-install-helm.html

#STEP2:Deploy an Elasticsearch cluster

kubectl apply -f elastic-pvc.yaml
kubectl apply -f demo-pv.yaml
kubectl apply -f elastic-storage-class.yaml

//Monitor cluster health and creation progress//
kubectl get elasticsearch

//You can see that one Pod is in the process of being started//
kubectl get pods --selector='elasticsearch.k8s.elastic.co/cluster-name=quickstart'

//Access the logs for that Pod//
kubectl logs -f quickstart-es-default-0

//Request Elasticsearch access//
kubectl get service quickstart-es-http

//Request the Elasticsearch endpoint//
curl -u "elastic:$(kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}')" -k "https://quickstart-es-http:9200"

#STEP2:Deploy a Kibana instance 

kubectl apply -f kiban-nodeport.yaml

//Access Kibana//
kubectl get service quickstart-kb-http

#STEP3:Deploy tomcat instance with filebeat sidecar

//Create filebeat.yml configmap//
kubectl apply -f filebeat-configmap.yaml

//Create tomcat container with filebeat attched//
kubectl apply -f tomcat-with-filebeat.yaml

//Expose tomcat deployment temporarly using nodePort//
kubectl apply -f tomcat-temp-expose.yaml

//Access Tomcat//
kubectl get service tomcat-service

#STEP5:Deploy a metricbeat instance
kubectl apply -f metricbeat-k8s.yaml



























