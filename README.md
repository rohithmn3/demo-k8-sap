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
1) create the pod                     # kubectl apply -f pod.yaml -n demo-ns --dry-run
2) verify that the pod is running     # kubectl get pods -n demo-ns
3) get the logs                       # kubectl logs <pod-name>
4) describe the pod                   # kubectl describe pod <pod-name>
5) label the pod                      # we can add labels under metadata of the yaml file
6) IP of the Pod                      # kubectl get pods -n demo-ns -o wide
  
