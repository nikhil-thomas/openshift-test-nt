# Q8. Find template named cakephp-mysql-persistent and deploy it and export the yamls

### create project q8


### create q8-app from template openshift/cakephhp-mysql-persistent
```
$ oc new-app  openshift/cakephp-mysql-persistent
```
output:
```
--> Deploying template "openshift/cakephp-mysql-persistent" for "openshift/cakephp-mysql-persistent" to project q8                                
                                                                                                                                                  
     CakePHP + MySQL                                                                                                                              
     ---------                                                                                                                                    
     An example CakePHP application with a MySQL database. For more information about using this template, including OpenShift considerations, see
 https://github.com/sclorg/cakephp-ex/blob/master/README.md.                                                                                      
                                                                                                                                                  
     The following service(s) have been created in your project: cakephp-mysql-persistent, mysql.                                                 
                                                                                                                                                  
     For more information about using this template, including OpenShift considerations, see https://github.com/openshift/cake-ex/blob/master/READ
ME.md.                                                                                                                                            
                                                                                                                                                  
     * With parameters:                                                                                                                           
        * Name=cakephp-mysql-persistent                                                                                                           
        * Namespace=openshift                                                                                                                     
        * PHP Version=7.1                                                                                                                         
        * Memory Limit=512Mi                                                                                                                      
        * Memory Limit (MySQL)=512Mi                                                                                                              
        * Volume Capacity=1Gi                                                                                                                     
        * Git Repository URL=https://github.com/sclorg/cakephp-ex.git                                                                             
        * Git Reference=                                                                                                                          
        * Context Directory=                                                                                                                      
        * Application Hostname=                                                                                                                   
        * GitHub Webhook Secret=7qknXUgcERVCnjLygMdLMTXy651S1aN5l8JFxWpl # generated                                                              
        * Database Service Name=mysql                                                                                                             
        * Database Engine=mysql                                                                                                                   
        * Database Name=default                                                                                                                   
        * Database User=cakephp                                                                                                                   
        * Database Password=k0nibeCimOaWyRBm # generated                                                                                          
        * CakePHP secret token=Ad2gVKiEaAfzgf89UbEnGOwzh9f4eBa13Wkm4gZjWlrQZ3Lny5 # generated
        * CakePHP Security Salt=EqVDsciWBETtY6DeQh0tDWvBUnSP7u7xJx1J5jiu # generated
        * OPcache Revalidation Frequency=2
        * Custom Composer Mirror URL=

--> Creating resources ...
    error: secrets "cakephp-mysql-persistent" already exists
    service "cakephp-mysql-persistent" created
    route "cakephp-mysql-persistent" created
    imagestream "cakephp-mysql-persistent" created
    buildconfig "cakephp-mysql-persistent" created
    deploymentconfig "cakephp-mysql-persistent" created
    error: persistentvolumeclaims "mysql" already exists
    service "mysql" created
    deploymentconfig "mysql" created
--> Failed
```

## check status
```
$ oc status
```
output:
```
In project q8 on server https://192.168.99.100:8443

http://cakephp-mysql-persistent-q8.192.168.99.100.nip.io (svc/cakephp-mysql-persistent)
  dc/cakephp-mysql-persistent deploys istag/cakephp-mysql-persistent:latest <-
    bc/cakephp-mysql-persistent source builds https://github.com/sclorg/cakephp-ex.git on openshift/php:7.1
      build #1 running for 8 seconds
    deployment #1 waiting on image or update

svc/mysql - 172.30.221.69:3306
  dc/mysql deploys openshift/mysql:5.7
    deployment #1 running for 7 seconds - 0/1 pods


1 info identified, use 'oc status -v' to see details.
```

### check buildlogs
```
$ oc logs -f cakephp-mysql-persistent-1-build 
```
output:
```
...(truncated)
Time: 145 ms, Memory: 12.00MB
OK (6 tests, 34 assertions)
Pushing image 172.30.1.1:5000/q8/cakephp-mysql-persistent:latest ...
Pushed 0/10 layers, 1% complete
Pushed 1/10 layers, 12% complete
Pushed 2/10 layers, 21% complete
Pushed 3/10 layers, 32% complete
Pushed 4/10 layers, 42% complete
Pushed 5/10 layers, 52% complete
Pushed 6/10 layers, 65% complete
Pushed 7/10 layers, 75% complete
Pushed 8/10 layers, 85% complete
Pushed 9/10 layers, 97% complete
Pushed 10/10 layers, 100% complete
Push successful
```

### list all resources
```
$ oc get all
```
output:
```
NAME                                         REVISION   DESIRED   CURRENT   TRIGGERED BY
deploymentconfigs/cakephp-mysql-persistent   1          1         0         config,image(cakephp-mysql-persistent:latest)
deploymentconfigs/mysql                      1          1         1         config,image(mysql:5.7)

NAME                                    TYPE      FROM      LATEST
buildconfigs/cakephp-mysql-persistent   Source    Git       1

NAME                                TYPE      FROM          STATUS     STARTED         DURATION
builds/cakephp-mysql-persistent-1   Source    Git@c1b7cdc   Complete   6 minutes ago   4m24s

NAME                                    DOCKER REPO                                   TAGS      UPDATED
imagestreams/cakephp-mysql-persistent   172.30.1.1:5000/q8/cakephp-mysql-persistent   latest    2 minutes ago

NAME                              HOST/PORT                                           PATH      SERVICES                   PORT      TERMINATION   WILDCARD
routes/cakephp-mysql-persistent   cakephp-mysql-persistent-q8.192.168.99.100.nip.io             cakephp-mysql-persistent   <all>                   None

NAME                                     READY     STATUS      RESTARTS   AGE
po/cakephp-mysql-persistent-1-build      0/1       Completed   0          6m
po/cakephp-mysql-persistent-1-deploy     1/1       Running     0          2m
po/cakephp-mysql-persistent-1-hook-pre   1/1       Running     3          2m
po/mysql-1-deploy                        1/1       Running     0          6m
po/mysql-1-llt79                         0/1       Running     0          6m

NAME                            DESIRED   CURRENT   READY     AGE
rc/cakephp-mysql-persistent-1   0         0         0         2m
rc/mysql-1                      1         1         0         6m

NAME                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
svc/cakephp-mysql-persistent   ClusterIP   172.30.140.91   <none>        8080/TCP   6m
svc/mysql                      ClusterIP   172.30.221.69   <none>        3306/TCP   6m
```

### export all as yaml
```
$ oc export all --as-template=exported-template > exported-template.yml
```

### delete all
```
$ oc delete all --all
```
output:
```
deploymentconfig "cakephp-mysql-persistent" deleted
deploymentconfig "mysql" deleted
buildconfig "cakephp-mysql-persistent" deleted
imagestream "cakephp-mysql-persistent" deleted
route "cakephp-mysql-persistent" deleted
pod "cakephp-mysql-persistent-1-build" deleted
pod "cakephp-mysql-persistent-1-deploy" deleted
pod "cakephp-mysql-persistent-1-hook-pre" deleted
pod "mysql-1-deploy" deleted
pod "mysql-1-llt79" deleted
service "cakephp-mysql-persistent" deleted
service "mysql" deleted
```
