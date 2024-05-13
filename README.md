# Reflection on Hello Minikube

## 1. Compare the application logs before and after you exposed it as a Service. Try to open the app several times while the proxy into the Service is running. What do you see in the logs? Does the number of logs increase each time you open the app?

![](imgfiles/Screenshot%202024-05-13%20170926.png)

For every time we run the app with `minikube service hello-node`, there will be a `GET` method called. Thus increasing the number of logs each time we open the app since there will be more `GET` method called.

## 2. Notice that there are two versions of `kubectl get` invocation during this tutorial section. The first does not have any option, while the latter has `-n` option with value set to `kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you explicitly created?

The `-n` option is used to specify the namespace when running `kubectl get`. Without the `-n`, it will use the default namespace. The output doesn't list the pods/services that I've created because it is created in the default namespace. For that output, it can be viewed by this command `kubectl get pods,services`.