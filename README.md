# istio-http-example

## Prepare
### kubernetes
You have to prepare kubernetes cluster. I'm using EKS on AWS.

### istioctl

```bash
$ curl -L https://git.io/getLatestIstio | sh -
$ cd istio-1.*
$ sudo cp bin/istio /usr/local/bin/
```

### helm

```bash
$ cd istio-1.*
$ kubectl create -f install/kubernetes/helm/helm-service-account.yaml
$ helm init --service-account tiller
```

### istio

```bash
$ kubectl create namespace istio-system
$ cd istio-1.*
$ helm install \
--wait \
--name istio \
--namespace istio-system \
install/kubernetes/helm/istio
...
$ helm status istio
LAST DEPLOYED: Fri Feb  1 22:40:42 2019
NAMESPACE: istio-system
STATUS: DEPLOYED
...
```
If you can find `istio-sidecar-injector`, istio auto injection is running.

```bash
$ kubectl get pods -n istio-system
NAME                                     READY   STATUS    RESTARTS   AGE
istio-citadel-7dd558dcf-ldb44            1/1     Running   0          17h
istio-egressgateway-88887488d-bp7qv      1/1     Running   0          17h
istio-galley-787758f7b8-txr4p            1/1     Running   0          17h
istio-ingressgateway-58c77897cc-qn554    1/1     Running   0          17h
istio-pilot-868cdfb5f7-znvjr             2/2     Running   0          17h
istio-policy-56c4579578-25ztf            2/2     Running   0          17h
istio-sidecar-injector-d7f98d9cb-gnckv   1/1     Running   0          17h
istio-telemetry-7fb48dc68b-4826f         2/2     Running   0          17h
prometheus-76db5fddd5-48zqq              1/1     Running   0          17h
```

## Run

```bash
$ kubectl apply -f namespace.yml
$ kubectl apply -f deployment-backend.yml
$ kubectl apply -f service-backend.yml
$ kubectl apply -f deployment-client.yml
```

If istio auto injection is running, you can find 2 containers per pod.

```bash
$ kubectl get pods -n istio-http-example
NAME                        READY   STATUS    RESTARTS   AGE
backend-0-5b4d8b696-2q5d7   2/2     Running   0          1m
backend-0-5b4d8b696-r8cvn   2/2     Running   0          1m
client-0-796bb44b64-m5rq4   2/2     Running   1          43s
```

You can confirm http connection.

```bash
$ kubectl logs -f client-0-796bb44b64-m5rq4 -n istio-http-example -c client
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
Hello, World!
```
