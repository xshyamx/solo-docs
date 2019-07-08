---
menuTitle: Tcp Proxy
title: Gloo as a TCP proxy
weight: 30
description: Example of configuring Gloo as a L4/TCP proxy.
---



## Tcp Proxy

In this tutorial, we'll take a look at using gloo as a TCP proxy. Envoy by default is an L4 proxy and is therefore
more than up to the task. Gloo's TCP routing feature are slightly different than the rest of Gloo's routing as a result
of the relative simplicity of TCP level routing. Current features include standard routing, ssl, and sni domain matching.

### Resources 

For reference on  the 

- [Gateway]({{% ref "/v1/github.com/solo-io/gloo/projects/gateway/api/v2alpha1/gateway.proto.sk" %}})
- [Proxy]({{% ref "/v1/github.com/solo-io/gloo/projects/gloo/api/v1/proxy.proto.sk" %}})

### What you'll need

You'll need to have Gloo installed on Kubernetes and have access to that Kubernetes cluster. Please refer to the
[Gloo installation]({{< ref "/installation" >}}) for guidance on installing Gloo into Kubernetes.

You'll also need access from the Kubernetes cluster to an external API. You will also need an accessible TCP service running 
in cluster, this may be anything, for the purposes of this tutorial we will use a basic tcp-echo pod.

Firstly apply the following yaml into the namespace of your choice. For the purposes of this tutorial we will be using `gloo-system`

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    gloo: testrunner
  name: testrunner
spec:
  containers:
  - image: soloio/testrunner:testing-8671e8b9
    imagePullPolicy: IfNotPresent
    command:
      - sleep
      - "36000"
    name: testrunner
  restartPolicy: Always
```
```bash
pbpaste | k apply -n gloo-system -f -
```

Once the `tcp-echo` pod is up and running we are ready to create our gateway resource and begin routing to it.