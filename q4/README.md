# Q4. Create a pod and secret (username and password), inject those secrets as env in your pod (logic of your choice)

### create project q4

### create a simple php app: https://github.com/nikhil-thomas/openshift-php-helloworld

```
oc new-app --name q4-app https://github.com/nikhil-thomas/openshift-php-helloworld.git
```
output:
```
..(truncated)
--> Creating resources ...
    imagestream "q4-app" created
    buildconfig "q4-app" created
    deploymentconfig "q4-app" created
    service "q4-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q4-app' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/q4-app' 
    Run 'oc status' to view your app.
```

### create a secret
```
$ oc create secret generic creds \
> --from-literal user=developer
> --from-literal password=mypassword
```
output:
```
secret "creds" created
```

### inspect secret creds
```
oc describe secret/creds
```
output:
```
Name:         creds
Namespace:    q4
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  10 bytes
user:      9 bytes
```

### add secret to q4-app deployment config as environment variables
```
$ oc set env dc/q4-app --from secret/creds
```
outpus:
```
deploymentconfig "q4-app" updated
```
### list environment variables in deployment config q4-app
```
$ oc set enc dc/q4-app --list
```
output:
```
# deploymentconfigs q4-app, container q4-app
# PASSWORD from secret creds, key password
# USER from secret creds, key user
```

### re-deploy q4-app
```
$ oc rollout latest dc/q4-app
```
output:
```
deploymentconfig "q4-app" rolled out
```

### expose q4-app service
```
$ oc expose svc/q4-app && oc get routes
```
output:
```
route "q4-app" exposed
NAME      HOST/PORT                         PATH      SERVICES   PORT       TERMINATION   WILDCARD
q4-app    q4-app-q4.192.168.99.100.nip.io             q4-app     8080-tcp                 None
```

### fetch index-env.php
```
$ curl q4-app-q4.192.168.99.100.nip.io/index-env.php
```
output:
```
<html>
 <head>
  <title>PHP Test</title>
 </head>
 <body>
 User: developer</p>Password: mypassword</p> </body>
</html>
```

## clean up
```
oc delete project q4
```
output:
```
project "q4" deleted
```
