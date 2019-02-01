# Q2. In q2 namespace, create imagestream for nodejs and deploy https://github.com/sclorg/nodejs-ex

create project q2
```
$ oc new-project q2
```
output:
```
Now using project "q2" on server "https://192.168.99.100:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git

to build a new example application in Ruby.
```

list image streams in q2
```
$ oc get is
```
output:
```
no resources found.
```

list image streams in openshift namespace and find nodejs image stream
```
$ oc get is -n openshift | grep nodejs
```
output:
```
nodejs       172.30.1.1:5000/openshift/nodejs       8,8-RHOAR,latest + 4 more...   30 hours ago
```

import nodejs image stream into project q2
```
$ oc import-image 172.30.1.1:5000/openshift/nodejs --confirm --insecure
```
output:
```
The import completed successfully.                                                                                             
                                                                                                                               
Name:                   nodejs                                                                                                 
Namespace:              q2                                                                                                     
Created:                1 second ago                                                                                           
Labels:                 <none>                                                                                                 
Annotations:            openshift.io/image.dockerRepositoryCheck=2019-02-01T15:05:44Z                                          
Docker Pull Spec:       172.30.1.1:5000/q2/nodejs                                                                              
Image Lookup:           local=false                                                                                            
Unique Images:          1                                                                                                      
Tags:                   1                                                                                                      
                                                                                                                               
latest                                                                                                                         
  tagged from 172.30.1.1:5000/openshift/nodejs                                                                                 
    will use insecure HTTPS or HTTP connections                                                                                
                                                                                                                               
  * 172.30.1.1:5000/openshift/nodejs@sha256:3cc041334eef8d5853078a0190e46a2998a70ad98320db512968f1de0561705e                   
      1 second ago

...(truncated)
```

list is in q2 project
```
$ oc get is
```
output:
```
NAME      DOCKER REPO                 TAGS      UPDATED
nodejs    172.30.1.1:5000/q2/nodejs   latest    5 seconds ago
```

deploy app
```
oc new-app --name q2-app https://github.com/sclorg/nodejs-ex
```
or
```
oc new-app --name q2-app q2/nodejs~https://github.com/sclorg/nodejs-ex
```
output:
```
--> Found image 93de123 (3 months old) in image stream "q2/nodejs" under tag "latest" for "nodejs" 

...(truncated)

--> Creating resources ...
    imagestream "q2-app" created
    buildconfig "q2-app" created
    deploymentconfig "q2-app" created
    service "q2-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q2-app' to track its progress.                                                        
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:  
     'oc expose svc/q2-app'
    Run 'oc status' to view your app.
```

expose q2-app service to create a route
```
$ oc expose svc/q2-app
```

output:
```
route "q2-app" exposed
```

get route
```
$ oc get routes
```
output:
```
NAME      HOST/PORT                         PATH      SERVICES   PORT       TERMINATION   WILDCARD
q2-app    q2-app-q2.192.168.99.100.nip.io             q2-app     8080-tcp                 None
```

test route with curl
```
$ curl q2-app-q2.192.168.99.100.nip.io
```
output:
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <title>Welcome to OpenShift</title>

...(truncated)

</head>                                                                                                                        
<body>                                                                                                                         
                                                                                                                               
<section class='container'>                                                                                                    
          <hgroup>                                                                                                             
            <h1>Welcome to your Node.js application on OpenShift</h1>                                                          
          </hgroup>

...(truncated)
                <h2>Request information</h2>
                <p>Page view count:

                   <span class="code" id="count-value">No database configured</span>                                          
                   </p>

          </section>
        </div>

        <footer>
          <div class="logo"><a href="https://www.openshift.com/"></a></div>                                                   
        </footer>
</section>
</body>
</html>
```

clean up
```
$ oc delete project q2
```
output:
```

```