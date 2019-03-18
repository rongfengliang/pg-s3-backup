# pg backup with s3 (minio demo)

> fork from https://github.com/Remigius2011/pg-dump && do some change 

## how to running

* start pg && minio server

```code
docker-compose up -d postgres  s3
```

* add some table && datas

```code
the way you like
```

* test pg backup

```code
docker-compose up backup
```

## with k8s cronjob

> you can use k8s cronjob for scheduler auto

* cronjob yaml content

```code
{
    "kind": "CronJob",
    "apiVersion": "batch/v1beta1",
    "metadata": {
      "name": "gitlab-backup-job"
    },
    "spec": {
      "schedule": "0 0 1 * *",
      "concurrencyPolicy": "Replace",
      "suspend": false,
      "jobTemplate": {
        "metadata": {
          "creationTimestamp": null
        },
        "spec": {
          "template": {
            "metadata": {
              "creationTimestamp": null,
              "labels": {
                "apprepositories.kubeapps.com/repo-name": "gitlab-backup-job"
              }
            },
            "spec": {
              "containers": [
                {
                  "name": "gitlab-pg-backup",
                  "image": "dalongrong/pg-dump-gzip",
                  "env": [
                    {
                        "name": "PG_DB",
                        "value": "postgres"
                    },
                    {
                        "name": "PG_HOST",
                        "value": "postgres"
                    },
                    {
                        "name": "PG_PASSWORD",
                        "value": "dalong"
                    },
                    {
                        "name": "PG_PORT",
                        "value": "5432"
                    },
                    {
                        "name": "S3_ACCESS_KEY",
                        "value": "dalongdemo"
                    },
                    {
                        "name": "S3_HOST",
                        "value": "s3:9000"
                    },
                    {
                        "name": "S3_PROTOCOL",
                        "value": "http"
                    },
                    {
                        "name": "S3_SECRET_KEY",
                        "value": "dalongdemo"
                    }
                ],
                  "imagePullPolicy": "IfNotPresent"
                }
              ],
              "restartPolicy": "OnFailure"
            }
          }
        }
      },
      "successfulJobsHistoryLimit": 3,
      "failedJobsHistoryLimit": 1
    }
  }

```
