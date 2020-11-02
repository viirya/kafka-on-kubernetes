# kafka-on-kubernetes

Running Kafka on Kubernetes. The is based on https://github.com/Yolean/kubernetes-kafka. The original repo looks complicated and contains many stuffs not sure to work or not. This repo simplifies it to only contain Kubernetes manifest that is necessary for running a Kafka cluster on Kubernetes.

## Docker images

To run Kafak, we need two Docker images in https://github.com/solsson/dockerfiles: kafka-initutils and kafka.

```
git clone https://github.com/solsson/dockerfiles
cd initutils
docker build -f Dockerfile -t <your docker repo>/kafka-initutils .
docker push <your docker repo>/kafka-initutils
cd ..
cd docker build -f Dockerfile -t <your docker repo>/kafka:2.6.0 . 
docker push <your docker repo>/kafka:2.6.0
```

## Launch Zookeeper + Kafka on Kubernetes

* Change namespace accordingly in K8S kustomization file, e.g., in variants/scale-1/kustomization.yaml.
* Change docker image names accordingly
* Launch Zookeeper + Kafka services, e.g., 1 zookeeper node + 1 kafka node

```
kubectl -n <your namespace> apply -k variants/scale-1
kubectl -n <your namespace> logs kafka-0
kubectl -n <your namespace> logs zoo-0
```

`variants/scale-6-9` can be used to launch a larger Kafka cluster. You can easily extend the manifest to increase the size of the cluster.

The Kubernetes manifests use PVC. For testing purpose, you can also change it to `emptyDir`, e.g.,
```
--  volumeClaimTemplates:    
-  - metadata:                                                                   
-      name: data     
-    spec:                
-      accessModes: [ "ReadWriteOnce" ]                                          
-      resources:                                                                
-        requests:       
-          storage: 1Gi   
+      - name: data                                                                                                                                               
+        emptyDir: {} 
```

# Test Kafka function with kafkacat utility

## Docker image
```
git clone https://github.com/edenhill/kafkacat.git
cd kafkacatdocker build -f Dockerfile.Debian -t <your docker repo>/kafkacat .
docker push <your docker repo>/kafkacat
```

## Launch kafkacat pods

```
# kafkacat includes a pod of producer, consumer
kubectl -n <your namespace> apply -k kafkacat
kubectl -n <your namespace> logs kafkacat-xxxx consumer
```
