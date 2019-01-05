# Chartmuseum

Chartmuseum is an open-source Helm Chart Repository server wirtten in Go, with support for cloud storage backends.

Now we using `minio` as a storage backend.

## Getting Started

### Installation

```sh
# on Linux
curl -LO https://s3.amazonaws.com/chartmuseum/release/latest/bin/linux/amd64/chartmuseum
chmod +x ./chartmuseum
mv ./chartmuseum /usr/local/bin
```

### Using with Minio

- setup your minio access key and secret key

```
$ export AWS_ACCESS_KEY_ID="..."
$ export AWS_SECRET_ACCESS_KEY="..."
```

- start chartmuseum

```
chartmuseum --debug --port=8080 --storage="amazon" --storage-amazon-bucket="<YOUR-BUCKET-NAME>" --storage-amazon-prefix="" --storage-amazon-endpoint="<MINIO-SERVER-URL>"
```

- add repo

```
helm repo add chartmuseum http://localhost:8080
```

Now you can use chartmuseum api to do something.

The Chartmuseum API you can see [offical documents](https://github.com/helm/chartmuseum#api).
