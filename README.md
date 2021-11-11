# Sidecar Proof of Concept

## Brief
With the upcoming work around converting the existing document composed graph service from Node to C#, the challenge we face is around the actual D3 graph code that's written in TypeScript and not easily able to be ported over to C#.

With that in mind, this is a quick POC that shows a working example of a single pod that has 2 containers within it. Currently they are both nginx containers, with the 'main' container exposing port 80 then forwarding all traffic up to the 'tools' container which is listening on port 81.

## Test
In order to prove that it works, you will need to have a local k8s cluster up and running. Once that is in place, you can apply the POC YAML configuration:
```sh
kubectl apply -f experimental.yaml
```

Confirm that the pod is up and running via `kubectl`:
```sh
kubectl get pods
```

You should see something like the below:
```sh
NAME                                            READY   STATUS      RESTARTS   AGE
i360-proto-54f8d44bb7-4ztn8                     2/2     Running     0          77m
```

As we've set up a service, you will now be able to hit the pod via the service IP. You can get the IP address for the service via `kubectl`:
```sh
kubectl get svc i360-service
```

From there, to prove that you are redirected to the 'tools' container, you can hit the service on port 80 and confirm that the test HTML ("This is the sidecar poc") is displayed from the 'tools' container. An easy way to do this is by spinning up a DNS tools container in your k8s cluster:
```sh
kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools
```

Then, once the DNS tools pod is up and running, you'll be presented with a command prompt. Curl the container as below:
```sh
curl <SERVICE_IP>:80
```
And you should see the response of the test HTML ("This is the sidecar poc")