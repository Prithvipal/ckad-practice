# ckad-practice

## Core Concepts

### Important commands

1. kubectl create -f file-name.yaml
2. kubectl replace -f file-name.yaml // to update the kubenetes object using existing file.
3. kubectl scale --replicas=6 -f replicaset-defination.yaml // changes the replicas using same file without modifying the file
4. kubectl scale --replicas=6 replicaset myapp-replicaset
5. kubectl run nginx --image nginx

### Notes:
1. Difference between replication controller and and replicaset is that replicaset can monitor existing pod which were created before replicaset. It find those pods by selector. This capability is not available in replication controller.
2. Deployment is superset of replicaset because it has all the capablities as replicaset and some extra like: rolling update, rollback(in case of error), pause and resume.