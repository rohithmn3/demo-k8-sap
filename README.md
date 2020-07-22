# demo-k8-sap
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

  
  
Exercise 1 - create your first pod
**************
```
1) create the pod                     # kubectl apply -f pod.yaml
2) verify that the pod is running     # kubectl get pods
3) get the logs                       # kubectl logs <pod-name>
4) describe the pod                   # kubectl describe pod <pod-name>
5) label the pod                      # we can add labels under metadata of the yaml file
6) IP of the Pod                      # kubectl get pods -n demo-ns -o wide
7) exec into pod                      # kubectl exec -it <pod_name> -- bash
8) delete the pod                     # kubectl delete pod <pod-name>

```
Exercise 2 - Deployment
**************
```
1) create the deployment:                                     # kubectl apply -f deployment.yaml
2) verify that the deployment is running                      # kubectl get deployments
3) check how the deployment is bringing up the pods           # kubectl rollout status deployment <dep_name>
   (its a rolling update meaning serial update - one by one, so that we are able to keep the application up and running)
4) scaling                                                    # kubectl scale deployment <dep_name> --replicas=2
5) Test the resilience of your deployment by deleting a pod.
6) update with wrong image tag                                # kubectl set image deployment/<dep_name> nginx=nginx:mainlne --record

7)                                                            # kubectl rollout status deployment <dep_name>
8)                                                            # kubectl get pods
9)                                                            # kubectl rollout history deployment <dep_name>
10) Rolling Back to a Previous revision                       #kubectl rollout undo deployment.v1.apps/<dep_name>
11)                                         # kubectl rollout history deployment.v1.apps/<dep_name> --revision=2


12) select the deployemnt based on labels   # kubectl get deployments --selector='app=nginx'
