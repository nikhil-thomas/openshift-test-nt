# Q9. Write a job which can runs 4 pods in parallel (logic of your choice)

### create job definition (job.yaml)
```
apiVersion: batch/v1
kind: Job
metadata:
  name: q9-job
spec:
  parallelism: 4
  completions: 1
  template:
    metadata:
      name: q9-template
      labels:
        app: q9
    spec:
      containers:
      - name: sleep5
        image: busybox
        command: ["sh", "-c", "echo start5; sleep 5; echo stop5"]
      - name: sleep6
        image: busybox
        command: ["sh", "-c", "echo start6; sleep 5; echo stop6"]
      - name: sleep7
        image: busybox
        command: ["sh", "-c", "echo start7; sleep 5; echo stop7"]
      - name: sleep8
        image: busybox
        command: ["sh", "-c", "echo start8; sleep 5; echo stop8"]
      restartPolicy: Never
```

### create job
```
$ oc create -f job.yaml
```
output:
```
job "q9-job" created

```

### check job status
```
$ oc get jobs
```
output:
```
NAME      DESIRED   SUCCESSFUL   AGE
q9-job    1         1            1m
```

### check pods
```
$ oc get pods
```
output:
```
NAME           READY     STATUS      RESTARTS   AGE
q9-job-pjjzq   0/4       Completed   0          1m
```

### check logs
```
$ oc logs q9-job-pjjzq -c sleep5
```
output:
```
start5
sleep5
```

### delete job
```
$ oc delete job q9-job
```
output:
```
job "q9-job" deleted
```

### Reference

* https://docs.okd.io/latest/dev_guide/jobs.html#overview

