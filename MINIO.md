
# Minio

S3 compatible server and gateway

Notes:

 * Minio without [federation](https://docs.minio.io/docs/minio-federation-quickstart-guide.html) is equivalent with one pipeline from HDFS or Ozone. All the servers are part of one replication group. Multiple replication group requires federation.

## Source

Key repositories:

 * https://github.com/minio/minio -- server
 * https://github.com/minio/mc -- client

## Components

## Metadata

Written in a `.minio.sys` buckets in json format:

Example:

```
.
├── buckets
│   ├── aaa
│   └── play
│       └── ApacheCon_ EU 19_ Patterns and Anti-Patterns to use Apache Bigdata in K8s.pdf
│           └── fs.json
├── config
│   ├── config.json
│   └── iam
│       └── format.json
├── format.json
├── multipart
└── tmp
    └── 6599a42f-e826-4c1f-a026-350de53ba131
```

Test Edit.
