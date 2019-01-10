Minio
---------------------------

Minio is an object storage server. The following section describe how to deploy minio on Kubernetes.

Now it only provider minio standalone server deployment.Distributed server will be added later.

## Standalone

If you want to deploy minio standalone server, you can copy standalone directory to your Kubernetes cluster.

Minio default namespace is `storage`, if you want to change namespace, you should update yaml `namespace` field manually.

If you want to custom your own access key and secret key, you should change [deployment.yaml](./standalone/deployment.yaml) envrionments.

### Use helm to install minio

You can use `helm` to install minio, you can choose one of ways to install minio:

- use `stable(https://kubernetes-charts.storage.googleapis.com)` repo to install minio
- use local minio chart

Now we use local minio chart to install standalone minio.

Installation steps:

1. clone minio chart to local

```sh
$ git clone -b minio https://github.com/pytimer/charts.git
```

2. copy charts/stable/minio chart to host that can connect your Kubernetes cluster.

3. setting minio custom configurations.

```yaml
image:
  repository: minio/minio
mcImage:
  repository: minio/mc

service:
  type: NodePort
  nodePort: 30002

persistence:
  existingClaim: minio-pv-claim

accessKey: "..."
secretKey: "..."
```

4. install minio

Because we use hostPath, so we can not use default pvc in minio chart, we should create pvc before we install minio.

```sh
$ kubectl apply -f namespace.yaml
$ kubectl apply -f storageclass.yaml
$ kubectl apply -f pv.yaml
$ kubectl apply -f pvc.yaml
```

If you run commands above, you can see pv and pvc resources in Kubernetes.

```sh
$ kubectl get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM     STORAGECLASS    REASON    AGE
minio-pv   100Gi      RWO            Retain           Available             local-storage             9s
$ kubectl get pvc -n storage
NAME             STATUS    VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS    AGE
minio-pv-claim   Pending                                       local-storage   12s
```

`helm install -f standalone/custom.yaml ./minio --namespace storage`

## Client

The minio client deployment is experimental.

`kubectl apply -f mc.yaml`

#### Usage

mc Dockerfile `ENTRYPOINT` is `mc`, so you can use `mc` command like below:

`kubectl exec -it -n storage minio-client-deployment-866df9555b-kfxc4 mc -- --help`

## Reference

[Deploy minio on Kubernetes](https://docs.minio.io/docs/deploy-minio-on-kubernetes)
