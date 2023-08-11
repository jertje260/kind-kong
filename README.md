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
`kubectl port-forward -n kong svc/kong-proxy 8000:80`

then you can do requests to the kong proxy service with localhost:8000

`curl localhost:8000`
should return something like:
```json
{
  "message":"no Route matched with those values"
}```

