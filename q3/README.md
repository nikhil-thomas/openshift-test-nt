# Q3. Create a pod and configmap (of some file), use configmap as a volume (logic of your choice)

### create project q3

### create a simple php app: https://github.com/nikhil-thomas/openshift-php-helloworld

```
oc new-app --name q3-app https://github.com/nikhil-thomas/openshift-php-helloworld.git
```
output:
```
..(truncated)
--> Creating resources ...
    imagestream "q3-app" created
    buildconfig "q3-app" created
    deploymentconfig "q3-app" created
    service "q3-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q3-app' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/q3-app' 
    Run 'oc status' to view your app.
```

### create a configmap
```
$ oc create configmap greeting \
> --from-literal message="Hello World from file"
```
output:
```
configmap "greeting" created
```

### inspect configmap greeting
```
oc describe cm/greeting
```
output:
```
Name:         greeting
Namespace:    q3
Labels:       <none>
Annotations:  <none>

Data
====
message:
----
Hello World from file
Events:  <none>
```

### mount config map as a file in deployment config
```
$ oc edit dc/q3-app
```
add volume mount
.spec.template.spec.containers
```
...(truncated)
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - name: config-volume
          mountPath: /opt/app-root/src/cm-path
      volumes:
      - name: config-volume
        configMap:
          name: greeting
      dnsPolicy: ClusterFirst
      restartPolicy: Always
...(truncated)
```
output:
```
deploymentconfig "q3-app" edited
```
### re-deploy q3-app
```
oc rollout latest dc/q3-app
```
output:
```
deploymentconfig "q3-app" rolled out
```
### expose q3-app service
```
oc expose svc/q3-app
```
output:
```
route "q3-app" exposed
```
```
oc get routes
```
output:
```
q3-app    q3-app-q3.192.168.99.100.nip.io             q3-app     8080-tcp                 None
```

### fetch mounted file path uisng
```
curl q3-app-q3.192.168.99.100.nip.io/cm-path/message
```
output:
```
Hello World from file 
```

### Reference

* https://docs.openshift.com/enterprise/3.2/dev_guide/configmaps.html#configmaps-use-case-consuming-in-volumes
 