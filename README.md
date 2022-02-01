# ckad-practice


## Important commands

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
11. `kubectl get configmap` // list config map
12. `kubectl get configmap <config-name> -o yaml` // show config map details in yaml format
13. `kubectl describe configmap <config-name>` // describes config map
14. `kubectl create secret generic app-secret  --from-literal=DB_HOST=mysql --from-literal=DB_USER=root` // creates secrets with provided values
15. `kubectl create secret generic app-secret  --from-file=app-secrets.properties` // create secret from properties file
16. `kubectl create serviceaccount dashboard-sa` // creates new service account and secrets for this service account
17. `kubectl taint nodes node1 app=blue:NoSchedule` // taints the node. blue is the taint value and NoSchedule is the taint-effect. There can be three possible values for taint-effect: NoSchedule, PreferNoSchedule, NoExecute
18. `kubectl taint nodes node1 node-role.kubernetes.io/master:NoSchedule-` to untaint a node
19. `kubectl label nodes <node-name> <label-key>=<label-value>` // to set label to the node. These labels can be used while adding nodeSelector in pod definition file.
20. `kubectl top nodes` // to view node performance metrics: cpu and memory consumption
21. `kubectl top pods` // to view pod performance metrics: cpu and memory consumption
22. `kubectl get pod --selector type=front-end` // gets all the pod by specified labels
23. `kubectl rollout status deploy/myapp` // shows rollout status
24. `kubectl rollout history deploy/myapp` // shows history and revisions of the deployment
25. `kubectl set image deploy/<deployment-name> <container-name>=<image-name-with-tag>` // Sets new image to container of deployment. Example: `kubectl set image deploy/myapp-deployment  nginx-container=nginx:1.9.1`
26. `kubectl rollout undo deploy/myapp` // undo the deployment
27. `kubectl rollout history deploy/myapp-deployment --revision=5` // Status of each revision individually
28. `kubectl set image deploy/myapp-deployment nginx=nginx:1.17 --record` // When you run kubectl rollout history command, it does not show the value for CHANGE-CAUSE column. If we want to set value for this column while changing deployment we can specify `--record` flag. Please note that you can specify this flag while running kubectl apply command.
29. `kubectl get job`
30. `kubectl get cronjob`
31. `kubectl create job <job-name> --image <image-name>`
32. `kubectl create cronjob <job-name> --image <image-name> --schedule <cron expression>`
33. `kubectl expose deployment simple-webapp-deployment --name=webapp-service --target-port=8080 --type=NodePort --port=8080` // create service on given deployments



## Desing Pattern: Multi container pod
- Sidecar
- Adaptor
- Ambassador

## Pod Conditions
- PodScheduled
- Initialized
- ContainerReady
- Ready

### Readiness Probe
When pod is deployed successfully, the pod condition change to Ready and service can start sending requests to the new pod.
But is not necessary that application running within the pod is actually ready to accept requests. For example some application takes few seconds to be start accepting request.
So we should not mark Pod as ready until application within the pod is actually ready.

This can be achieved by readiness probe. example pod definition is in dir of this repo: observability/readiness-probe/readiness-pod-def.yaml file

There are three posssible way to set readiness probe:
- Http Get
```yaml
readinessProbe:
    httpGet:
        path: /api/ready
        port: 8080
```
- TCP Socket
```yaml
readinessProbe:
    tcpSocket:
        port: 3306
```
- Command execution
```yaml
readinessProbe:
    exec:
        command:
          - cat
          - /app/is_ready
```

There are other option in readiness probe like: `initialDelayProbe`, `periodSeconds` and `failureThreshold`
```yaml
readinessProbe:
    httpGet:
        path: /api/ready
        port: 8080
    initialDelayProbe: 10 # to set initial delay to check probes
    periodSeconds: 5
    failureThreshold: 8
```

There are three posssible way to set liveliness probe. example pod definition is in dir of this repo: observability/liveness-probe/liveness-pod-def.yaml file

### liveness probe
It is useful to check if container within the pod is healthy. It test container periodically. If test failed the it restart the container.



Q: What does it mean that container is healthy? <br>
A: Following are the cases: <br> 
- In case of webapp, the api server is up and running. HTTP Get - /api/healthy
- In case of database, we can check if a particular TCP is listening. TCP test - 3306
- Or simply execute a command to perform a test.

- Http Get
```yaml
livenessProbe:
    httpGet:
        path: /api/ready
        port: 8080
```
- TCP Socket
```yaml
livenessProbe:
    tcpSocket:
        port: 3306
```
- Command execution
```yaml
livenessProbe:
    exec:
        command:
          - cat
          - /app/is_ready
```

## Deployment Strategy
- Recreate
- Rolling Update

The deployment strategy of existing deployment can be seen by describe command. You will find the field called `StrategyType` in the output of describe command.

## Service Type
- **NodePort:** Valid range 30000 - 32767. It access pod from host IP.
- **ClusterIP:** Used for service to service call. It is a default Service type
- LoadBalancer

## Notes:
1. Difference between replication controller and and replicaset is that replicaset can monitor existing pod which were created before replicaset. It find those pods by selector. This capability is not available in replication controller.
2. Deployment is superset of replicaset because it has all the capablities as replicaset and some extra like: rolling update, rollback(in case of error), pause and resume. Also, when we create new deployment, it automatically creates a new replicaset.