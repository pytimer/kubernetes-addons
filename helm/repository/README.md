Charts repository
-------------------------------------

A chart repository is an HTTP server that houses an `index.yaml` file and optionally some packaged charts. It allows upload yourself charts and share others.

Because a chart repository can be any HTTP server that serve YAML and tar files and can answer GET requests, so you have a plethora of options when deploy your own chart repository. For example, Google Cloud Storage, Amazon S3, Github Pages, Minio, or even create your own web server.

## The chart repository structure

A chart repository consists of packaged charts and a special file called `index.yaml` which contains an index of all charts in the repository.

Recommand that `index.yaml` also hosted on the same server with charts.

## Deploy

The charts repository depend on an HTTP server, this tutorial use `minio` as backend storage and provide HTTP feature. 

### Minio

#### Deploy minio server

The `minio` server deploy on the Kubernetes you can reference to [deploy documents](../../minio). Minio server on the Kubernetes expose service using `NodePort`, and default port is `30002`.

#### Create minio buckets

Create a buckets on minio for charts repository. Create a bucket that names `charts` as the charts repository directory.

You should download [minio client binary](https://dl.minio.io/client/mc/release) file before you operate minio if you don't want use minio web ui.

If your host os is Linux amd64, you can use below command to download client binary.

```sh
wget https://dl.minio.io/client/mc/release/linux-amd64/mc
chmod +x ./mc
mv ./mc /usr/bin/
```

`mc config host add kminio http://10.33.46.210:30002 kubernetescloud kubernetescloudstorage`

### 

