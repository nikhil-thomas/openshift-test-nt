# Q1. Deploy https://github.com/sclorg/nodejs-ex on openshift in q1 namespace/project

create q1 project/namespace
```
$ oc new-project q1
```
output:
```
Now using project "q1" on server "https://192.168.99.100:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git

```

create new app to deploy nodejs-ex
```
$ oc new-app --name q1-app https://github.com/sclorg/nodejs-ex
```

output:
```
--> Found image 93de123 (3 months old) in image stream "openshift/nodejs" under tag "10" for "nodejs"

    Node.js 10.12.0
    ---------------
    Node.js  available as docker container is a base platform for building and running various 

    ... (truncated)

    * The source repository appears to match: nodejs
    ... (truncated)
--> Creating resources ...
    imagestream "q1-app" created
    buildconfig "q1-app" created
    deploymentconfig "q1-app" created
    service "q1-app" created
--> Success
    Build scheduled, use 'oc logs -f bc/q1-app' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/q1-app'
    Run 'oc status' to view your app.
```

expose q1-app service to create a route
```
$ oc expose svc/q1-app
```

output:
```
route "q1-app" exposed
```

get route
```
$ oc get routes
```
output:
```
NAME      HOST/PORT                         PATH      SERVICES   PORT       TERMINATION   WILDCARD
q1-app    q1-app-q1.192.168.99.100.nip.io             q1-app     8080-tcp                 None
```

test route with curl
```
$ curl q1-app-q1.192.168.99.100.nip.io
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
$ oc delete project q1
```
output:
```

```