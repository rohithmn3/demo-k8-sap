# Exercises for K8
```
# export KUBECONFIG=$HOME/.kube/config
# mkdir $HOME/.kube/custom-contexts
# export KUBECONFIG=$HOME/.kube/custom-contexts/kube-config-k8-cluster1.yml:$HOME/.kube/config
# kubectl config get-contexts
# kubectl config use-context <new-context-name>

Change the default namespace in k8:
# kubectl config set-context --current --namespace=my-namespace
# kubectl config view | grep namespace:

```

  
  
## Exercise 1 - create your first pod
**************
[pod.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/pod.yaml)
```
1) create the pod                     # kubectl apply -f pod.yaml
2) verify that the pod is running     # kubectl get pods
3) get the logs                       # kubectl logs <pod-name>
4) describe the pod                   # kubectl describe pod <pod-name>
5) label the pod                      # we can add labels under metadata of the yaml file
6) IP of the Pod                      # kubectl get pods -o wide
7) exec into pod                      # kubectl exec -it <pod_name> -- bash
8) delete the pod                     # kubectl delete pod <pod-name>

```


## Exercise 2 - Deployment
**************
[deployment.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/deployment.yaml)
```
1) create the deployment:                                     # kubectl apply -f deployment.yaml
2) verify that the deployment is running                      # kubectl get deployments
3) check how the deployment is bringing up the pods           # kubectl rollout status deployment <dep_name>
   (its a rolling update meaning serial update - one by one, so that we are able to keep the application up and running)
4) scaling                                                    # kubectl scale deployment <dep_name> --replicas=2
5) Test the resilience of your deployment by deleting a pod.
6) update with wrong image tag (mainlne)                      # kubectl set image deployment/<dep_name> nginx=nginx:mainlne --record

7)                                                            # kubectl rollout status deployment <dep_name>
8)                                                            # kubectl get pods
9)  Check the rollout history of the deploymen                # kubectl rollout history deployment <dep_name>
10) Rolling Back to a Previous revision                       #kubectl rollout undo deployment.v1.apps/<dep_name>
11) Rolling Back to a specific revision/version               # kubectl rollout history deployment.v1.apps/<dep_name> --revision=2


12) select the deployemnt based on labels                     # kubectl get deployments.apps -l run=web (we get the o/p if we have labeled the deployemnt)
```


## Exercise 3 - Services
**************
[service.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/service.yaml)                                            
[ingress.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/ingress.yaml)                              

Now that the application is running and resilient; let's make it available to other users inside and outside of the cluster.

```
# kubectl create -f service.yaml
# kubectl get svc

# kubectl create -f ingress.yaml
# kubectl get ingress
```
Now try to access the ingress ADDRESS, which we get from the above command. (ex: a3b287aa82e9c42dfbda7bb7ec83b8e6-1053580487.eu-central-1.elb.amazonaws.com)

To try a service of type LoadBalancer, From where we can directly access our application from the LB address; Follow below steps:
```
- modify the type to LoadBalancer here: https://github.com/rohithmn3/demo-k8-sap/blob/master/service.yaml#L13
  # kubectl apply -f service.yaml
  # kubectl get svc -o wide
- No need of ingress here..!
- Try accessing the application from browser : http://<loadbalancer-ip>
```


## Exercise 4 - PVC                                     
************** 
[storageclass.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/storageclass.yaml)                                                                         
[pvc.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/pvc.yaml)                                              
[pvc_deployment.yaml](https://github.com/rohithmn3/demo-k8-sap/blob/master/pvc_deployment.yaml)

persist data in kubernetes using volumes.                                                                              
                                                                   
PV - storage chunk created by the storage admin.                                                                                   
PVC - is a request made to the storage by the developers.                                                                             
                                                                    
**Two ways of Provesioning**                                 
Static Provesioning : PV needs to be created before PVC                                        
Dynamic Provesioning: PV is created at the same time of PVC                                          
                                                    
**Remember:**                                                
volumeBindingMode : WaitForFirstConsumer                                                 
(i.e.., This will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created)                           

In here we will try to attach volume to the pod using **Dynamic Provesioning**                                          
```
1) create a storage class                                                                       # kubectl create -f storageclass.yaml
2) check the status od the storage class                                                        # kubectl get storageclass
3) create a PVC and make sure we point it to the above created storage class                    # kubectl create -f pvc.yaml
4) check the status of the PVC to understand volumeBindingMode                                  # kubectl get pvc
                                                                                                # kubectl describe pvc <pvc_name>

4) Modify the Deployment created in Exercise 2 by attaching the created volumes using PVC
            # cp pvc_deployment.yaml deployment.yaml;
            # kubectl apply -f deployment.yaml
            # kubectl get deployments

5) check whether the data will be consistant in the pods by following the below steps:
            # kubectl get pods 
            # kubectl exec -it <pod_name> -- sh
                    /# df -h (check your newly created mount)
                    /# mkdir test (create a dummy folder/file inside the mount) 
                    /# exit (exit from the conatiner)
            
            # kubectl delete pod <pod_name> (Delete the pod)
            once the new pod is back up and running login to it and verify whether the same folder/file is still present. If yes then the storage is persistant.
            
```


## Exercise 5 - StatefulSets                                     
************** 
- Represent a set of Pods with unique, persistent identities and stable hostnames.
- The state information and other resilient data for any given StatefulSet Pod is maintained in persistent disk storage associated with the StatefulSet.

**Assignemt** : Try to combine our service.yaml, deployment.yaml and pvc.yaml to create a statefulset.yaml and observe the order in which pods are coming up and their staeble hostnames.                                     
For example : https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#components
