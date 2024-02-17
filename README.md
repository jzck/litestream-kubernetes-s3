Inspired by [linkding on fly.io](https://github.com/fspoettel/linkding-on-fly)

## Storing the state in s3

You'll an s3 compatible bucket, like backblaze, aws, cloudflare or any compatible provider. Litestream will restore the bucket in an `emptyDir`. Then during operation a `litestream` sidecar will live replicate the changes to s3.

## Contents of `manifest.yaml`

 - a `Namespace` for the project
 - a `ConfigMap` containing `litestream.yml` config
     - replace `$S3_ENDPOINT` with your bucket config
 - a `StatefulSet` containing
     - an `initContainer` to restore the sqlite db
     - a `Pod` with `linkding` + `litestream` official images
 - a `Service` to expose the pods
     - I'm using tailscale annotations, replace these with traefik or your favorite ingress controller stuff.

## Setup

```
$ export S3_ENDPOINT=<REPLACE ME>
$ export S3_BUCKET=<REPLACE ME>
$ export S3_PATH=<REPLACE ME>
$ export S3_ACCESS_KEY_ID=<REPLACE ME>
$ export S3_SECRET_ACCESS_KEY=<REPLACE ME>
$ envsubst <manifest.yaml | kubectl apply -f -
$ kubectl create secret -n linkding generic litestream-s3 \
    --from-literal=LITESTREAM_ACCESS_KEY_ID=$S3_ACCESS_KEY_ID \
    --from-literal=LITESTREAM_SECRET_ACCESS_KEY=$S3_SECRET_ACCESS_KEY
```
