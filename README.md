# openshift-test-nt
openshift test solutions (nikhil thomas)

## solutions

1. [Q1. Deploy https://github.com/sclorg/nodejs-ex on openshift in q1 namespace/project](/q1/README.md)

2. [Q2. In q2 namespace, create imagestream for nodejs and deploy https://github.com/sclorg/nodejs-ex](/q2/README.md)

3. [Q3. Create a pod and configmap (of some file), use configmap as a volume (logic of your choice)](/q3/README.md)

4. [Q4. Create a pod and secret (username and password), inject those secrets as env in your pod (logic of your choice)](/q4/README.md)

5. [Q5. Create a deployment which has init container, init container should create some file and which should be available in main pod. (logic of your choice)](/q5/README.md)

6. [Q6. Create a pod with non-persistent volume](/q6/README.md)

7. [Create a pod and use NodePort service to expose it](/q7/README.md)

8. Find template named cakephp-mysql-persistent and deploy it and export the yamls

9. Write a job which can runs 4 pods in parallel (logic of your choice)

## theory

### Pod vs Deployment

**Pod:** is the smallest deployable resource/object in kubernetes/openshift. A Pod has one or more containers. Each Pod will have a unique IP within a cluster. Containers in a Pod will share POD IP.

**Deployment:** defines a deployment startegy/lifecycle for a set of Pods under a replication controller. In general, each deploymet can be considered as individual application services in a cluster.

---

### Buildconfig and S2I

A buildconfig is a resource which describes the build strategy of a application. It can use different build strategies such as docker, S2I, pipeline etc.

S2I is a process/tool to build a container image without writing a conventional Dockerfile. GEnerally, S2I process has following components.

* A builder image: an image which provides applcation runtime, dependency management tools etc.
* S2I scripts: assemble, run, usage etc defines how the application source code is built/run in the builder image. Theses scripts can be customized in by adding assemble/run/usage scripts within .s2i/bin folder in the applcaiton repository
* Final application image; the output of S2I process is the application container image

(S2I can also be used as an independent too to build images)

---

### Imagestream

An Imagestream is mechanism to associate a short/convinient name to the hash of an image within openshift. Imagestreams are refernces to an image in an external registry or an image in an internal registry or another image stream. At any point in time an imagestream points to an image has (not an image tag). Hence, imagestreams ensures that expected/right images are used by safegaurding against ambigous tag updates (eg: latest, latest release or latest build ?)

---

### Liveness Probes and Readiness Probes

**Readiness probes:** is a mechanism to ensure that a pod is ready to accept load. If a pod doesnot respond witha positive readiness check traffic will not be routed to it.

**Liveness probes:** is a mechanism to ensure that a pod is capable of processing load. If a pod fails liveness check no more traffic is routed to it and it will be killed.

**note:** both liveness probes and readiness probes can be implemented using HTTP-API-endpoints or HTTP connection response or TCP sockets, 

---
