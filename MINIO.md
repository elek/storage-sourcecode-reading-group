
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


## Erasure coding

This is the default distributed write (no 3-way replication).


[server-main.go](https://github.com/minio/minio/blob/master/cmd/server-main.go)
```
// Initialize object layer with the supplied disks, objectLayer is nil upon any error.
func newObjectLayer(ctx context.Context, endpointServerSets EndpointServerSets) (newObject ObjectLayer, err error) {
	// For FS only, directly use the disk.
	if endpointServerSets.NEndpoints() == 1 {
		// Initialize new FS object layer.
		return NewFSObjectLayer(endpointServerSets[0].Endpoints[0].Path)
	}

	return newErasureServerSets(ctx, endpointServerSets)
}
```

Main read/write logic is in the [erasure-object.go](https://github.com/minio/minio/blob/master/cmd/erasure-object.go).

 * Write is successful if written to the quorum (of disks).
 * Roles of disks are shuffled (parity vs data) for each keys (no block level segmentation)

Metadata for write: 

```
dd if=out2/xl.meta skip=8 bs=1 | msgpack2json -d
310+0 records in
310+0 records out
310 bytes copied, 0.000288978 s, 1.1 MB/s
{
    "Versions": [
        {
            "Type": 1,
            "V2Obj": {
                "ID": <bin of size 16>,
                "DDir": <bin of size 16>,
                "EcAlgo": 1,
                "EcM": 3,
                "EcN": 2,
                "EcBSize": 10485760,
                "EcIndex": 5,
                "EcDist": [
                    5,
                    1,
                    2,
                    3,
                    4
                ],
                "CSumAlgo": 1,
                "PartNums": [
                    1
                ],
                "PartETags": [
                    ""
                ],
                "PartSizes": [
                    216773700
                ],
                "PartASizes": [
                    216773700
                ],
                "Size": 216773700,
                "MTime": 1607974278087111556,
                "MetaSys": {},
                "MetaUsr": {
                    "content-type": "application/octet-stream",
                    "etag": "35205fa56cbd73c2fe37f9d33e3a4788"
                }
            }
        }
    ]
}
```


Metadata is written first, and data is moved to the final location "commit":

```

	// Write unique `xl.meta` for each disk.
	if onlineDisks, err = writeUniqueFileInfo(ctx, onlineDisks, minioMetaTmpBucket, tempObj, partsMetadata, writeQuorum); err != nil {
		return ObjectInfo{}, toObjectErr(err, bucket, object)
	}

	// Rename the successfully written temporary object to final location.
	if onlineDisks, err = renameData(ctx, onlineDisks, minioMetaTmpBucket, tempObj, fi.DataDir, bucket, object, writeQuorum, nil); err != nil {
		return ObjectInfo{}, toObjectErr(err, bucket, object)
	}
```

## Open questions

 * Why O_DIRECT is required?
