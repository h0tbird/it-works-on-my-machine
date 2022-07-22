# it-works-on-my-machine
Local k8s on arm64

### Local pull-through registries

List images in pull-through registries:
```
curl -s 192.168.64.1:5001/v2/_catalog | jq
curl -s 192.168.64.1:5002/v2/_catalog | jq
curl -s 192.168.64.1:5003/v2/_catalog | jq
```

List image tags in pull-through registries:
```
curl -s 192.168.64.1:5001/v2/calico/cni/tags/list | jq
curl -s 192.168.64.1:5002/v2/argoproj/argocd/tags/list | jq
curl -s 192.168.64.1:5003/v2/resf/istio/pilot/tags/list | jq
```

Get manifest for a given image tag:
```
curl -s http://192.168.64.1:5002/v2/argoproj/argocd/manifests/v2.4.7 | jq
```

### Calico

Commands to manage Calico:
```
calicoctl get ippool -o wide
```

### Cluid-init

Tail cloud-init logs:
```
multipass exec kube-00 -- tail -f /var/log/cloud-init-output.log
multipass exec kube-01 -- tail -f /var/log/cloud-init-output.log
multipass exec kube-02 -- tail -f /var/log/cloud-init-output.log
```

### Envoy config in VMs

Status of the certificates in the VM:
```
multipass exec virt-01 -- curl -s localhost:15000/config_dump | istioctl pc secret --file -
```

### Tcpdump

Tcpdump traffic to port `8080`:
```
k --context kube-01 -n httpbin exec -it httpbin-69d46696d6-c6p6m -c istio-proxy -- sudo tcpdump dst port 8080 -A
k --context kube-02 -n httpbin exec -it httpbin-7f859459c6-lkfbr -c istio-proxy -- sudo tcpdump dst port 8080 -A
```

Send requests to the service above:
```
k --context kube-01 -n httpbin exec -it sleep-5f694bf9d6-vqbfv -- curl http://httpbin.httpbin:5000/get
k --context kube-02 -n httpbin exec -it sleep-74456b78d-8hwd7 -- curl http://httpbin.httpbin:5000/get
```
