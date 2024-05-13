# Reflection on Hello Minikube

## 1. Compare the application logs before and after you exposed it as a Service. Try to open the app several times while the proxy into the Service is running. What do you see in the logs? Does the number of logs increase each time you open the app?

![](imgfiles/Screenshot%202024-05-13%20170926.png)

For every time we run the app with `minikube service hello-node`, there will be a `GET` method called. Thus increasing the number of logs each time we open the app since there will be more `GET` method called.

## 2. Notice that there are two versions of `kubectl get` invocation during this tutorial section. The first does not have any option, while the latter has `-n` option with value set to `kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you explicitly created?

The `-n` option is used to specify the namespace when running `kubectl get`. Without the `-n`, it will use the default namespace. The output doesn't list the pods/services that I've created because it is created in the default namespace. For that output, it can be viewed by this command `kubectl get pods,services`.

# Reflection on Rolling Update & Kubernetes Manifest File

## 1. What is the difference between Rolling Update and Recreate deployment strategy?

In a Rolling Update deployment strategy, Kubernetes gradually replaces old instances of a resource such as pods with new ones. It ensures that a specified number of replicas are available at all times during the update process. However, in a Recreate deployment strategy, Kubernetes first terminates all existing instances of a resource and then creates new ones with the updated configuration or image.

## 2. Try deploying the Spring Petclinic REST using Recreate deployment strategy and document your attempt.

* ![](imgfiles/Screenshot%202024-05-13%20224149.png)
First, I edit the `deployment.yaml ` file so the deployment type will be `Recreate`. Then I deploy it.

* ![](imgfiles/Screenshot%202024-05-13%20230120.png)
Then, I try to simulate by updating the image then verify the update status.

* ![](imgfiles/Screenshot%202024-05-13%20230445.png)
By seeing the results here, we know that recreate deployment type terminates all existing pods before creating new ones since the value of `OldReplicaSets` is none.

## 3. Prepare different manifest files for executing Recreate deployment strategy.

Here is the line of code for my Recreate deployment strategy file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{"deployment.kubernetes.io/revision":"4"},"creationTimestamp":"2024-05-13T13:25:15Z","generation":5,"labels":{"app":"spring-petclinic-rest"},"name":"spring-petclinic-rest","namespace":"default","resourceVersion":"6031","uid":"25aba1fc-d2c3-409f-8a46-862724e8eb10"},"spec":{"progressDeadlineSeconds":600,"replicas":4,"revisionHistoryLimit":10,"selector":{"matchLabels":{"app":"spring-petclinic-rest"}},"strategy":{"type":"Recreate"},"template":{"metadata":{"creationTimestamp":null,"labels":{"app":"spring-petclinic-rest"}},"spec":{"containers":[{"image":"docker.io/springcommunity/spring-petclinic-rest:3.2.1","imagePullPolicy":"IfNotPresent","name":"spring-petclinic-rest","resources":{},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File"}],"dnsPolicy":"ClusterFirst","restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{},"terminationGracePeriodSeconds":30}}},"status":{"availableReplicas":4,"conditions":[{"lastTransitionTime":"2024-05-13T13:37:30Z","lastUpdateTime":"2024-05-13T13:37:30Z","message":"Deployment has minimum availability.","reason":"MinimumReplicasAvailable","status":"True","type":"Available"},{"lastTransitionTime":"2024-05-13T13:25:15Z","lastUpdateTime":"2024-05-13T13:52:34Z","message":"ReplicaSet \"spring-petclinic-rest-54f476f68\" has successfully progressed.","reason":"NewReplicaSetAvailable","status":"True","type":"Progressing"}],"observedGeneration":5,"readyReplicas":4,"replicas":4,"updatedReplicas":4}}
  creationTimestamp: "2024-05-13T15:40:37Z"
  generation: 1
  labels:
    app: spring-petclinic-rest
  name: spring-petclinic-rest
  namespace: default
  resourceVersion: "2496"
  uid: 2b94a2ca-a218-4e14-b450-1431779769f7
spec:
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: spring-petclinic-rest
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: spring-petclinic-rest
    spec:
      containers:
      - image: docker.io/springcommunity/spring-petclinic-rest:3.2.1
        imagePullPolicy: IfNotPresent
        name: spring-petclinic-rest
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 4
  conditions:
  - lastTransitionTime: "2024-05-13T15:40:40Z"
    lastUpdateTime: "2024-05-13T15:40:40Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2024-05-13T15:40:37Z"
    lastUpdateTime: "2024-05-13T15:40:40Z"
    message: ReplicaSet "spring-petclinic-rest-54f476f68" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 4
  replicas: 4
  updatedReplicas: 4
```

## 4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster

Using Kubernetes manifest files, we can define the application according to our desire. This makes it easier to manage the infrastructure configuration of the app.