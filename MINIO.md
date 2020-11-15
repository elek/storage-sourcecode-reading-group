
# Minio

S3 compatible server and gateway

Notes:

 * Minio without [federation](https://docs.minio.io/docs/minio-federation-quickstart-guide.html) is equivalent with one pipeline from HDFS or Ozone. All the servers are part of one replication group. Multiple replication group requires federation.
 * MinIO's erasure coded backend uses high speed HighwayHash checksums to protect against Bit Rot. (source)[https://docs.min.io/docs/minio-erasure-code-quickstart-guide]
 * Disk '/tmp/m1' does not support O_DIRECT flags, MinIO erasure coding requires filesystems with O_DIRECT support
 * No HB, server state is [checked during startup](https://github.com/minio/minio/blob/4c773f7068fc7fd058c701f29b37cb2a3088e72f/cmd/bootstrap-peer-server.go).

## Source

Key repositories:

 * https://github.com/minio/minio -- server
 * https://github.com/minio/mc -- client

## Components

TODO

## 

Running services:

 * Only on HTTP port (9000)
 * Point to point connection between all servers


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

```
Format seems to be this one:

// The []journal contains all the different versions of the object.
//
// This array can have 3 kinds of objects:
//
// ``object``: If the object is uploaded the usual way: putobject, multipart-put, copyobject
//
// ``delete``: This is the delete-marker
//
// ``legacyObject``: This is the legacy object in xlV1 format, preserved until its overwritten
//
// The most recently updated element in the array is considered the latest version.

// Backend directory tree structure:
// disk1/
// └── bucket
//     └── object
//         ├── a192c1d5-9bd5-41fd-9a90-ab10e165398d
//         │   └── part.1
//         ├── c06e0436-f813-447e-ae5e-f2564df9dfd4
//         │   └── part.1
//         ├── df433928-2dcf-47b1-a786-43efa0f6b424
//         │   └── part.1
//         ├── legacy
//         │   └── part.1
//         └── xl.meta
```

Related source: https://github.com/minio/minio/blob/267d7bf0a9f114065314a0b2863f7fcc9e923012/cmd/xl-storage-format-v2.go

## Open questions

 * Is three way replication still supported or only EC?
 * Where is the code which handles the server2server replications
 * Why O_DIRECT is required?