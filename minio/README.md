Minio
---------------------------

Minio is an object storage server. The following section describe how to deploy minio on Kubernetes.

Now it only provider minio standalone server deployment.Distributed server will be added later.

## Standalone

If you want to deploy minio standalone server, you can copy standalone directory to your Kubernetes cluster.

Minio default namespace is `storage`, if you want to change namespace, you should update yaml `namespace` field manually.


## Reference

[Deploy minio on Kubernetes](https://docs.minio.io/docs/deploy-minio-on-kubernetes)