# Q6. Create a pod with non-persistent volume

### create project q6

### create q6-app (build image)
```
$ oc new-app --name q6-app https://github.com/nikhil-thomas/openshift-php-helloworld.git
```
output:
```
...(truncated)
--> Creating resources ...
    imagestream "q6-app" created
    buildconfig "q6-app" created
    deploymentconfig "q6-app" created
    service "q6-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q6-app' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/q6-app' 
    Run 'oc status' to view your app.
```

### list image streams
```
$ oc get is
```
output:
```
NAME      DOCKER REPO                 TAGS      UPDATED
q6-app    172.30.1.1:5000/q6/q6-app   latest    About a minute ago
```

### write pod definition with non persistent volume
```
vim pod.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  name: q6-pod
  namespace: q6
  labels:
    app: q6
spec:
  containers:
  - image: 172.30.1.1:5000/q6/q6-app
    name: q6-httpd-container
    volumeMounts:
    - name: temp-volume
      mountPath: /opt/app-root/src/temp-vol
  volumes:
  - name: temp-volume
    emptyDir: {}
```

### create pod
```
$ oc create -f pod.yaml
```
output:
```
pod "q6-pod" created
```

### check mount path
```
$ oc rsh q6-pod ls /opt/app-root/src
```
output:
```
LICENSE  README.md  index-env.php  index.php  temp-vol
```

### add a file in /opt/app-root/src/temp-vol
```
$ oc rsh q6-pod /bin/bash
```
```
bash-4.2$ echo "Hello World" > /opt/app-root/src/temp-vol/message
```
```
bash-4.2$ curl q6-pod:8080/temp-vol/message

Hello World
```
```
bash-4.2$ exit
$
```

### kill and recreate pod
```
$ oc delete pod/q6-pod
```
output:
```
pod "q6-pod" deleted
```

### recreate pod
```
$ oc create -f pod.yaml
```
output:
```
pod "q6-pod" created
```
### try to access /opt/app-root/src/temp-vol/message
```
$ oc rsh q6-pod /bin/bash
```
```
bash-4.2$ curl q6-pod:8080/temp-vol/message

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>404 Not Found</title>
</head><body>
<h1>Not Found</h1>
<p>The requested URL /temp-vol/message was not found on this server.</p>
</body></html>
```
```
bash-4.2$ exit
$
```

### cleanup
```
$ oc delete all --all
```
output:
```
deploymentconfig "q6-app" deleted
buildconfig "q6-app" deleted
imagestream "q6-app" deleted
pod "q6-app-1-build" deleted
pod "q6-app-1-tntt8" deleted
pod "q6-pod" deleted
service "q6-app" deleted
```

### Reference

* https://docs.openshift.com/enterprise/3.2/dev_guide/configmaps.html#configmaps-use-case-consuming-in-volumes
