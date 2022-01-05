# ckad-practice

## Core Concepts

### Important commands

1. kubectl create -f file-name.yaml
2. kubectl replace -f file-name.yaml // to update the kubenetes object using existing file.
3. kubectl scale --replicas=6 -f replicaset-defination.yaml // changes the replicas using same file without modifying the file
4. kubectl scale --replicas=6 replicaset myapp-replicaset
5. kubectl run nginx --image nginx
