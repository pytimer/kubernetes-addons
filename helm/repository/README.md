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

#### Set up minio client (Option)

Create a buckets on minio for charts repository. Create a bucket that names `charts` as the charts repository directory.

You should download [minio client binary](https://dl.minio.io/client/mc/release) file before you operate minio if you don't want use minio web ui.

If your host os is Linux amd64, you can use below command to download client binary.

```sh
wget https://dl.minio.io/client/mc/release/linux-amd64/mc
chmod +x ./mc
mv ./mc /usr/bin/
```

#### Add minio server to local configuration file

```sh
set +o history
mc config host add kminio http://<ip>:30002 kubernetescloud kubernetescloudstorage
set -o history
```

#### Create minio buckets

Create a buckets for charts repository on the minio. for example, create a bucket that names `charts`.

`mc mb kminio/charts`

If created success, you can see the console:

```sh
Bucket created successfully `kminio/charts`.
```

#### Setting bucket public permission

Because minio bucket default can't donwload without authenticate, so we should set the bucket download public permission.

`mc policy download kminio/charts`

### Charts repository

If you already have storage backend, you can create your own charts repository now.

#### Set up helm client

Installing helm client have many ways, you can read this [install docs](https://github.com/helm/helm/blob/master/docs/install.md#installing-the-helm-client) and choose one.

#### Create `index.yaml`

If the helm client installed, you need to create `index.yaml` for the charts repository.

The command is `helm repo index .`, if you run, a file that name `index.yaml` will be created in the current path.

```yaml
# `index.yaml` content
apiVersion: v1
entries: {}
generated: 2019-01-02T18:38:26.929522888+08:00
```

#### Upload `index.yaml`

You should upload the `index.yaml` to your storage backend, for this tutorial it is `minio`.

`mc cp ./index.yaml kminio/charts`

#### Testing your own charts repository

If you upload `index.yaml` successfully, you can testing your own charts repository.

- clone examples

We use the helm offical examples to test charts repository.

`git clone https://github.com/helm/helm.git`

- create new charts

```sh
$ helm package helm/docs/examples/alpine/
$ mkdir charts
$ mv alpine-0.1.0.tgz charts/
$ wget http://<ip>:30002/charts/index.yaml -o charts/index.yaml
$ helm repo index --merge charts/index.yaml --url http://<ip>:30002/charts charts/
```

Now you can see the charts directory have two files.

```sh
$ ll charts/
-rw-r--r-- 1 root root 1276 Jan  2 19:09 alpine-0.1.0.tgz
-rw-r--r-- 1 root root  467 Jan  2 19:10 index.yaml
```

- upload the new charts

```sh
mc cp charts/alpine-0.1.0.tgz kminio/charts
mc cp charts/index.yaml kminio/charts
```

Now your minio bucket that names `charts` have these files. you can add helm repo and get your chart.

- add helm repo

`helm repo add test http://<ip>:30002/charts`

- list all test repo charts

`helm search test`

```sh
$ helm search test
NAME            CHART VERSION   APP VERSION     DESCRIPTION
stable/alpine   0.1.0           3.3             Deploy a basic Alpine Linux pod
```

