# Setup KIND with KONG with db

## install KIND:
on windows run:
`choco install kind`

create kind cluster:
`kind create cluster --config config.yaml`

get your clusters:
`kind get clusters`
result:
```
kong
```

connect with kubectl:
`kubectl cluster-info --context kind-kong`

## Kong
```
kubectl create namespace kong
kubectl apply -f kong-postgress.yaml
```

this forwards the requests to the kong service.
`kubectl port-forward -n kong svc/kong-proxy 80:80`

then you can do requests to the kong proxy service with localhost:80

`curl localhost:80`
should return something like:
```json
{
  "message":"no Route matched with those values"
}
```

The admin api of kong is hosted on port 8444(https)
you can use the following command to forward it to port 8444
`kubectl port-forward -n kong svc/kong-proxy 8444:8444`


Everything is running, however we need a service which we can call, for this, there is the echo service. This can be added by running the following command:
```
kubectl apply -f echo.yaml
```

now a request like `curl -i http://kong.example/echo --resolve kong.example:80:127.0.0.1`
should return the following information:
```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
Content-Length: 144
Connection: keep-alive
Date: Fri, 08 Sep 2023 14:08:08 GMT
X-Kong-Upstream-Latency: 0
X-Kong-Proxy-Latency: 1
Via: kong/3.3.1

Welcome, you are connected to node kong-control-plane.
Running on Pod echo-74d47cc5d9-64nks.
In namespace default.
With IP address 10.244.0.8.
```

Attached is a postman collection, but postman doesn't have an option for the `--resolve` in curl, so to make this work, we will need to set it in the hosts file.
On windows it is here: `c:\Windows\System32\Drivers\etc\hosts`, on linux `/etc/hosts`

After this, a request to `kong.example` will point to your local cluster.

Time to add the `auth-key` plugin, so we can secure the echo service.
