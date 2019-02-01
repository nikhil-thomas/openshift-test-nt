# Q6. Create a pod with non-persistent volume

### create project q7

### create q7-app (build image)
```
$ oc new-app --name q7-app https://github.com/nikhil-thomas/openshift-php-helloworld.git
```
### write pod definition
```
vim pod.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
  name: q7-pod
  namespace: q7
  labels:
    app: q7-pod
spec:
  containers:
  - image: 172.30.1.1:5000/q7/q7-app
    name: q7-php-container
    ports:
    - containerPort: 8080
      protocol: TCP
```

### create pod
```
$ oc create -f pod.yaml
```
output:
```
pod "q7-pod" created
```

### write service definition
```
vim service.yaml
```
```
apiVersion: v1
kind: Service
metadata:
  name: q7-service
  namespace: q7
  labels:
    app: q7-pod
spec:
  selector:
    app: q7
  type: NodePort
  ports:  
  - port: 8080
    nodePort: 31080
```

### create service
```
$ oc create -f service.yaml
```
output:
```
service "q7-service" created
```

### expose q7-service
```
$ oc expose svc/q7-service
```
output:
```
route "q7-service" exposed
```

### get route
```
$ oc get routes
```
output:
```
NAME         HOST/PORT                             PATH      SERVICES     PORT      TERMINATION   WILDCARD
q7-service   q7-service-q7.192.168.99.100.nip.io             q7-service   8080                    None
```

## access route
```
$ curl q7-service-q7.192.168.99.100.nip.io
```
output:
```
<html>
 <head>
  <title>PHP Test</title>
 </head>
 <body>
 <p>Hello World Update 2</p> 
 </body>
</html>
```

## clean up
```
$ oc delete all --all
```
output:
```
deploymentconfig "q7-app" deleted
buildconfig "q7-app" deleted
imagestream "q7-app" deleted
route "q7-service" deleted
pod "q7-app-1-wfkqp" deleted
pod "q7-pod" deleted
service "q7-app" deleted
service "q7-service" deleted
```

### Reference

* https://docs.openshift.com/container-platform/3.6/dev_guide/expose_service/expose_internal_ip_nodeport.html
