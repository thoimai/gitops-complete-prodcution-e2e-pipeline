# GitOps 
* The Continuous Deployment (CD) part by deploying a Docker image to a Kubernetes environment using ArgoCD
* Using Jenkins for building and pushing the Docker image 

## ArgoCD 



## Kubernetes 



### 2. **Test Connectivity to the API Server**
You can test connectivity to the API server using `curl`. Try both HTTP and HTTPS to see if one of them responds:

```bash
curl http://34.130.233.75:6443/version
curl https://34.130.233.75:6443/version
```

### 3. **Check Cluster Configuration**
If you have access to the Kubernetes master node, verify the API server configuration to ensure that the correct URL and port are being used. Look for the `--bind-address` and `--secure-port` flags in the API server configuration or command-line arguments.

### 4. **Consider Regenerating the Kubeconfig File**
If you have access to the Kubernetes master node, you may want to regenerate the `kubeconfig` file using a command like:

```bash
kubectl config view --raw > /path/to/new/kubeconfig.yaml
```

Then copy this file to the ArgoCD VM and use it as the `kubeconfig`.

---
## Connect ArgoCD (argocd's VM) to Kubernetes Cluster (K8s's VM)

```log
argocd@argocd:~/.kube$ kubectl --kubeconfig=/home/argocd/.kube/app-cluster get nodes
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   5d1h   v1.27.4+k3s1
```

## Set the KUBECONFIG Environment Variable

`argocd@argocd:~$ export KUBECONFIG=/home/argocd/.kube/app-cluster`

```log
argocd@argocd:~$ kubectl get nodes 
NAME   STATUS   ROLES                  AGE     VERSION
k8s    Ready    control-plane,master   5d21h   v1.27.4+k3s1
```

## Check ArgoCD cluster pods 

```log
argocd@argocd:~/.kube$ kubectl --kubeconfig=/home/argocd/.kube/config get pods -n argocd 
NAME                                                READY   STATUS    RESTARTS       AGE
argocd-redis-b5d6bf5f5-qbgrr                        1/1     Running   5 (11m ago)    6d21h
argocd-notifications-controller-6786586cb-6h84v     1/1     Running   5 (11m ago)    6d21h
argocd-dex-server-6c6dfb6597-2frwp                  1/1     Running   5 (11m ago)    6d21h
argocd-repo-server-6658f8b96d-fpxv4                 1/1     Running   5 (11m ago)    6d21h
argocd-server-5fff657769-2665g                      1/1     Running   5 (11m ago)    6d21h
argocd-application-controller-0                     1/1     Running   5 (11m ago)    6d21h
argocd-applicationset-controller-74bd4b8497-46v75   1/1     Running   10 (11m ago)   6d21h
```

## Check ArgoCD cluster's services 

```log 
argocd@argocd:~/.kube$ kubectl --kubeconfig=/home/argocd/.kube/config get svc -n argocd 
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.43.205.187   <none>        7000/TCP,8080/TCP            6d21h
argocd-dex-server                         ClusterIP   10.43.61.183    <none>        5556/TCP,5557/TCP,5558/TCP   6d21h
argocd-metrics                            ClusterIP   10.43.56.196    <none>        8082/TCP                     6d21h
argocd-notifications-controller-metrics   ClusterIP   10.43.108.17    <none>        9001/TCP                     6d21h
argocd-redis                              ClusterIP   10.43.117.224   <none>        6379/TCP                     6d21h
argocd-repo-server                        ClusterIP   10.43.144.182   <none>        8081/TCP,8084/TCP            6d21h
argocd-server-metrics                     ClusterIP   10.43.153.117   <none>        8083/TCP                     6d21h
argocd-server                             NodePort    10.43.212.173   <none>        80:31380/TCP,443:31852/TCP   6d21h
```

### K8s Nodes
```log
k8s@k8s:~/.kube$ kubectl get nodes 
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   5d1h   v1.27.4+k3s1
```

## Add K8s Cluster to ArgoCD 

```log 
argocd@argocd:~/.kube$ argocd cluster add default --kubeconfig /home/argocd/.kube/app-cluster --name app-cluster
WARNING: This will create a service account `argocd-manager` on the cluster referenced by context `default` with full cluster level privileges. Do you want to continue [y/N]? y
INFO[0002] ServiceAccount "argocd-manager" created in namespace "kube-system" 
INFO[0002] ClusterRole "argocd-manager-role" created    
INFO[0002] ClusterRoleBinding "argocd-manager-role-binding" created 
INFO[0007] Created bearer token secret for ServiceAccount "argocd-manager" 
Cluster 'https://34.130.233.75:6443' added
```

## List connected K8s cluster to ArgoCD 

```log 
argocd@argocd:~/.kube$ argocd cluster list
SERVER                          NAME         VERSION  STATUS   MESSAGE                                                  PROJECT
https://34.130.233.75:6443      app-cluster           Unknown  Cluster has no applications and is not being monitored.  
https://kubernetes.default.svc  in-cluster            Unknown  Cluster has no applications and is not being monitored.
```


# What's Next after deploying containerized application successfully

Fantastic! The log shows that your containerized application has been successfully deployed to the Kubernetes cluster using ArgoCD. The application is running in the "default" namespace, and there are two pods (`complete-prodcution-e2e-deployment-7b568b8cf-sxgwr` and `complete-prodcution-e2e-deployment-7b568b8cf-trmqf`) that are in the "Running" status.

Here's what you might want to do next:

### 1. **Inspect the Application**

You can use `kubectl` to inspect the details of the deployed resources, such as the services, deployments, and pods:

```bash
kubectl describe deployment complete-prodcution-e2e-deployment
kubectl describe pods -l app=complete-prodcution-e2e
```

### 2. **Access the Application**

If your application exposes a web interface or API, you'll want to make sure it's accessible. Check the services to find the appropriate endpoint:

```bash
kubectl get svc
```

### 3. **Monitor the Application**

You may want to set up monitoring and logging to keep an eye on the application's performance and behavior. Tools like Prometheus and Grafana are commonly used for this purpose.

### 4. **Implement Continuous Updates**

Since you're using ArgoCD and the GitOps model, you can easily update the application by pushing changes to the Git repository. ArgoCD can be configured to automatically sync the changes or notify you when changes are detected.

### 5. **Scale the Application**

If needed, you can scale the application up or down by adjusting the number of replicas:

```bash
kubectl scale deployment complete-prodcution-e2e-deployment --replicas=3
```

### 6. **Backup and Disaster Recovery**

Consider implementing backup and disaster recovery strategies to ensure the application's data is safe.

### Conclusion

Congratulations on successfully deploying your containerized application using ArgoCD and Kubernetes! You've set up a modern, scalable, and maintainable deployment pipeline. If you have any further questions or need assistance with anything else, please don't hesitate to ask.
