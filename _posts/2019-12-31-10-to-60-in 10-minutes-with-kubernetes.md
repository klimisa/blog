---
layout: post
title: 0 to 60 in 10 minutes with Kubernetes
---

### Architecture

Kubernetes Scheduler is reposible to spread the pods to available nodes.

> TODO: In which way pods are failing.

### Namespace

TODO: Write something about the isolation they provide.

### Kubernetes resources

Everything in Kubernetes is resource and is controlled with an API.
That's really awesome and pretty much developers friendly.

### Labels

Show labels
`kubectl get pods --show-labels`

List label as column
`kubectl get pods -L app`

Add additional labels
`kubectl label pod <pod-name> type=special`

Edit labels
`kubectl label pod <pod-name> app=foo --overwrite`

### Nodes

Add labels in order to categorize node px. disk=ssd for node that have ssd disks.

Add label
`kubectl label node <node-name> disk=ssd`

### Pods

Delete all pods
`kubectl delete po --all`

### Replicaton Controllers

_State_ in Replication Controller is the number of pods.

When a RC sees more pods deletes the part of when it sees too few creates additional pods.

Scaling means declarative saying _change the desire state_ to the contoller rather to K8s.
You’re just specifying the desired state.

Delete the RC withoud deleting the pods.
`kubectl delete rc <rc-name> --cascade=false`

### Replica Sets

Replica Sets are the same as the Replicaton Controllers but with an advance matching/selector mechanism.

It has two selectors types,

- matchLabels
- matchExpressions
  - In
  - NotIn
  - Exists
  - DoesNotExist

You can specify both selectors types and should all evaluate to true.

### DaemonSet

Creates one pod in every node. This is useful in the case where for example you want a log collector on every node.
Use `node selector` in the spec.template.spec to specify which nodes to deploy pods.

> Tip: Be careful if the selector finds no nodes nothing will be deployed.

### Jobs

Pods that don't restart when they exit. They run once.

### Scheduler

Schedule a pod to run at a specific time interval.

### Services

Expose a group of pods through a single IP.

`kubectl expose`

or with yml.

> Tip: With `kubectl exec` you can remotely run command inside a container.

#### Discovery

Clients of service can access it via enviroment variable if they have started before the service.

You can also discover services via DNS

Run bash inside a container
`kubectl exec -it <pod-name> bash`

Use can use name ports in the service if you have named the ports pods.

```yml
kind: Pod
spec:
containers:
  - name: kubia
    ports:
      - name: http
        containerPort: 8080
      - name: https
        containerPort: 8443
```

There a resource in between of service and pods which is called Endpoint.
Decoupling service endpoints from service allows us to point to external servers.

There are three ways to expose services outside the cluster

- NodePort
- LoadBalancer
- Ingress

`NodePort` opens a port on each node where traffic can goes through.

```yml
apiVersion: v1
kind: Service
metadata:
  name: kubia-nodeport
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30123
  selector:
    app: kubia
```

`LoadBalancer` adds a load balancer in front of nodes in the case of node failure.

> NOTE: Pods are included as endpoints of a service if their labels match the service’s pod selector.

If a readiness check fails does not kill the pod. This an important difference from liveness.

> Tip: Check for connectivity for readiness ex. app -> database server

> NOTE: Changing a replication controller template has no effect on the running pods.

On readiness success pods will be added to the `EndPoints`.

> TIP: Always include a readiness even if it's a simple one. Do not include readiness when app is deleled or stopped.

### Volumes

Volumes share the same lifecycle as the pod.

> TODO: What's the difference between Servive and NodePort?

> TODO: How will I share volume paths between Win and Mac

> NOTE: PersistentVolumes don’t belong to any namespace. They’re cluster-level resources like nodes.

Claims can only be used by pods in the same namespace.

Dynamic volumes with storage classes.

### Configuration

Passing values to pods with

- Command-line arguments
- Enviroment variables
- Configuation files with a special type of volume ex. gitRepo

### Metadata

Passing data down to the pod with the `Downward API`.

### Deployments

this command change the image of any resource that has containers (rc, rs, deploy etc.)
`kubectl set image deployment kubia <name>=<image>`

A rollout occurs only when a change to pod template have been made.
