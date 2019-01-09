# Securing Helm installation

The Helm default installation applies **no security configurations**. It often use this type of installation when you working local development with Minikube or with a cluster that no secured.

About more securing helm installation, you can read the offical document that [Securing your Helm Installation](https://docs.helm.sh/using_helm/#securing-your-helm-installation).

## RBAC

Recent versions of Kubernetes employ a role-based access control (or RBAC) system to help mitigate the damage that can be done if credentials are misused or bugs exist.So if your Tiller deploy on the Kubernetes with RBAC enabled, you should add RBAC to Tiller.

The sections about RBAC, support some ways to setting Tiller RBAC. However, now this tutorial only support `cluster-admin` role.

### Service account with cluster-admin role

Create your own sevice account and rbac configuration.

In `rbac-config.yaml`:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

*Note: The cluster-admin role is created by default in Kubernetes cluster, so you don't have to define it.*

```sh
$ kubectl apply -f rbac-config.yaml
serviceaccount/tiller created
clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Now you run command `helm init --stable-repo-url http://<ip>:30002/charts --service-account tiller`

If you want to use your own tiller image, you can set `--tiller-image` if you run `helm init`.

If init successfully, you should wait tiller pod running.

```sh
$ kubectl get pod -n kube-system | grep tiller
tiller-deploy-87986794-jzsqm                           1/1       Running   0          51m
```

Tiller starting logs:

```sh
$ kubectl logs -n kube-system tiller-deploy-87986794-jzsqm
[main] 2019/01/02 13:54:36 Starting Tiller v2.12.1 (tls=false)
[main] 2019/01/02 13:54:36 GRPC listening on :44134
[main] 2019/01/02 13:54:36 Probes listening on :44135
[main] 2019/01/02 13:54:36 Storage driver is ConfigMap
[main] 2019/01/02 13:54:36 Max history per release is 0
```

## FAQ

### `helm reset` can not delete tiller completely.

Now `helm reset` have a question that can not delete tiller completely, so we should run two commands order to delete completely after reset.

```sh
$ kubectl delete deployment tiller-deploy --namespace=kube-system
$ kubectl delete service tiller-deploy --namespace=kube-system
```

You can read this issue to know more information [helm reset --force reports OK but tiller is not deleted ](https://github.com/helm/helm/issues/4825).
