# ckad-practice

## Core Concepts

### Important commands

1. `kubectl create -f file-name.yaml`
2. `kubectl replace -f file-name.yaml` // to update the kubenetes object using existing file.
3. `kubectl scale --replicas=6 -f replicaset-defination.yaml` // changes the replicas using same file without modifying the file
4. `kubectl scale --replicas=6 replicaset myapp-replicaset`
5. `kubectl run nginx --image nginx`
6. `kubectl get all` // get all the kubernetes objects
7. `kubectl get all -A` // get all the kubernetes objects of all the namespaces
8. `kubectl config set-context $(kubectl config current-context) --namespace=dev` // to set namespace as default for all kubectl command. In this case, we dont need to specify -n dev in kubect get pods command to get pods of dev namepsace. If we do this then we need to specify -n default to get objects from default namespace.
9. `kubectl create configmap <config-name>` // creates configmap with given name
10. `kubectl create configmap <config-name> --from-literal=APP_COLOR=blue --from-literal=APP_MOD=prod` // creates configmap with given name and configs

### Notes:
1. Difference between replication controller and and replicaset is that replicaset can monitor existing pod which were created before replicaset. It find those pods by selector. This capability is not available in replication controller.
2. Deployment is superset of replicaset because it has all the capablities as replicaset and some extra like: rolling update, rollback(in case of error), pause and resume. Also, when we create new deployment, it automatically creates a new replicaset.