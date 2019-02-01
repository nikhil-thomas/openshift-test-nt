# Q5. Create a deployment which has init container, init container should create some file and which should be available in main pod. (logic of your choice)

### create project q5

### create q5-app (build image)
```
$ oc new-app --name q5-app https://github.com/nikhil-thomas/openshift-php-helloworld.git
```
output:
```
...(truncated)
--> Creating resources ...
    imagestream "q5-app" created
    buildconfig "q5-app" created
    deploymentconfig "q5-app" created
    service "q5-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q5-app' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/q5-app' 
    Run 'oc status' to view your app.
```

### list image streams
```
$ oc get is
```
output:
```
NAME      DOCKER REPO                 TAGS      UPDATED
q5-app    172.30.1.1:5000/q5/q5-app   latest    About a minute ago
```

### write pod definition with init container
```
vim pod.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  name: app-init
  namespace: q5
  labels:
    app: app-init
spec:
  containers:
  - image: 172.30.1.1:5000/q5/q5-app
    name: q5-main
    volumeMounts:
    - name: data-volume
      mountPath: /opt/app-root/src/data
  initContainers:
  - name: data-volume
    image: alpine
    command: ['sh', '-c', 'echo "hello world" > /data/message']
    volumeMounts:
    - name: data-volume
      mountPath: /data
  volumes:
  - name: data-volume
    emptyDir: {}

```

### create pod
```
$ oc create -f pod.yaml
```
output:
```
pod "app-init" created
```

### check mount path
```
$ oc rsh app-init cat /opt/app-root/src/message
```
output:
```
hello world
```

### cleanup
```
$ oc delete all --all
```
output:
```
deploymentconfig "q5-app" deleted
buildconfig "q5-app" deleted
imagestream "q5-app" deleted
pod "app-init" deleted
pod "q5-app-1-x7v6b" deleted
service "q5-app" deleted
```

### Reference

* https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/

* https://docs.openshift.com/container-platform/3.3/architecture/core_concepts/containers_and_images.html#init-containers
