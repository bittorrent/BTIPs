
```
BTIP: 25
title: Integrate with S3-Compatible API service
author: Steve Zhang<steve.zhang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/25
status: Final
type: Core Protocol
category (*only required for Core Protocol): S3-Compatible API
created: 2023-07-06
```

## Simple Summary

Add an AWS S3-Compatible API service on BTFS protocol implementation.

## Abstract

An adaptation layer compatible with the AWS S3 API is created in the BTFS protocol implementation. This layer exposes external-facing AWS S3-compatible API services and translates incoming AWS S3 object operations into corresponding operations for BTFS resources.

## Motivation

AWS S3 is widely adopted as a second-generation centralized cloud storage solution, allowing multitudinous applications to access it through its API protocol or SDKs. In contrast, BTFS as a third-generation decentralized storage protocol has yet to gain popularity, which means developers must redesign and develop their applications to be compatible with BTFS's API protocol. However, with a set of AWS S3-compatible API services from BTFS, it would be far easier for applications to be connected to BTFS or use it as an optional transparent storage layer.

## Specification

###  Add configuration and start command options

In the configuration file, you can now find a new section called S3CompatibleAPI which includes three options:

- Enable: This option determines whether to enable the S3-compatible API. Please note that its priority is lower than the daemon start command option.
- Address: Use this option to configure the service address for the S3-compatible API. Keep in mind that it can be accessed externally when the address is non-local.
- HTTPHeaders: Use this option to configure the response headers for the S3-compatible API service. It can also be used to set up cross-domain rules.

```text
   {
     ...
     "S3CompatibleAPI": {
       "Enable": false,
       "Address": "127.0.0.1:6001",
       "HTTPHeaders": null
     },
     ...
   }
```

Additionally, we have introduced a new bool-type option 's3-compatible-api' in the start command. This option allows you to enable or disable the S3API service. Set it to 'true' to start the service or 'false' to prevent it from starting. By default, it is not set, meaning that the value will be determined by the 'EnableS3API' field in the configuration file.

```shell
  # enable the s3-compatible-api server
  btfs daemon --s3-compatible-api
  btfs daemon --s3-compatible-api=true
  
  # disable the s3-compatible-api server
  btfs daemon --se-compatible-api=false
  
  # enable or disable s3-compatible-api server according to the S3CompatibleAPI.Enable field in the configuration file.
  btfs daemon
```

### Access Keys related commands. 

Add access key-related commands. Access keys are used for authenticating S3 API access requests, and each access key record consists of the key, secret, root, enable, and created_at fields:

- key: the id of the access-key.
- secret: the secret of the access-key.
- enable: indicates whether to enable the access-key, the default is true, when the value is false, the request corresponding to the access-key will be invalid.
- is_deleted: indicates whether the access-key has been deleted.
- created_at: the creation time of the access-key record.
- updated_at: the update time of the access-key record.

#### 1. Generate Access Key
```shell
btfs accesskey generate
{"created_at":"2023-08-07T11:40:01.687592+08:00","enable":true,"is_deleted":false,"key":"d3ffb975-7f86-458c-ac09-76e976c8ca56","secret":"HK4A72rwC3XGWvqPBH2ZYrx4s2drSsIT","updated_at":"2023-08-07T11:40:01.687592+08:00"}
```

#### 2. Enable Access key
```shell
btfs accesskey enable <key>
```

#### 3. Disable Access Key
```shell
btfs accesskey disable <key>
```

#### 4. Reset Access Key
```shell
btfs accesskey reset <key>
```
Note: this operation will only reset the secret associated with the key.

#### 5. Delete Access Key
```shell
btfs accesskey delete <key>
```

#### 6. Get Access Key
```shell
btfs accesskey get <key>
{"created_at":"2023-08-07T11:40:01.687592+08:00","enable":true,"is_deleted":false,"key":"d3ffb975-7f86-458c-ac09-76e976c8ca56","secret":"I5qW3xIdwYI4EzE51zx7UErK89BQvpN5","updated_at":"2023-08-07T11:43:15.624528+08:00"}
```

#### 7. List all Access Keys
```shell
btfs accesskey list
[{"created_at":"2023-07-27T22:09:28.849721+08:00","enable":true,"is_deleted":false,"key":"afe9fbcd-5b05-4287-822b-5077e4ec48f7","secret":"V7PQE2N5YqsODDQH6RYpVMP0k1aZ5SN4","updated_at":"2023-07-27T22:10:53.804098+08:00"},{"created_at":"2023-08-07T11:40:01.687592+08:00","enable":true,"is_deleted":false,"key":"d3ffb975-7f86-458c-ac09-76e976c8ca56","secret":"I5qW3xIdwYI4EzE51zx7UErK89BQvpN5","updated_at":"2023-08-07T11:43:15.624528+08:00"}]
```

### Authentication

The BTFS S3-compatible API only supports [AWS v4 signatures (AWS4-HMAC-SHA256)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) for authentication and does not support AWS v2 signatures at this time.

### Access Control Lists (ACLs)

- The BTFS S3-compatible API provides limited support for access control lists (ACLs), with no current support for object-level ACLs.
- The methods GetObjectAcl and GetBucketAcl will work as expected, but GetObjectAcl will return the ACL of the bucket it is in.
- A bucket's owner is the access key used to create the bucket (access keys cannot be changed at present), and only the owner can change the ACL of a bucket or delete the bucket.
- Supported predefined ACLs include private, public-read, and public-read-write; see AWS ACL for detailed definitions; permissions, by default, are set to public-read.
    - __private__: only the bucket owner can upload, delete, and read objects in the bucket, as well as read the list of the objects inside the bucket.
    - __public-read__: only the bucket owner can upload and delete objects in the bucket; however, public users, including anonymous ones, can read objects and the list of the objects inside the bucket.
    - __public-read-write__: public users, including anonymous ones, can upload, delete, and read objects in the bucket, as well as read the list of the objects inside the bucket.

### Supported API methods

#### 1. Supported Bucket Calls:

- CreateBuket
- HeadBucket
- ListBuckets
- DeleteBucket
- PutBucketAcl
- GetBucketAcl

#### 2. Supported Object Calls:

- ListObjects
- ListObjectsV2
- HeadObject
- PutObject
- CopyObject
- DeleteObject
- DeleteObjects
- GetObjectAcl

#### 3. Supported Multipart Calls:

- CreateMultipartUpload	
- AbortMultipartUpload
- CompleteMultipartUpload
- UploadPart

### Methods for obtaining BTFS Hash

You can get btfs hash from object metadata and response header.
- Object metadata key:

```json
{
   "Metadata": {
      "Cid": "<BTFS cid>"
   }
}
```
- Response header
```
Cid=<BTFS Cid>
```

## Rationale

#### 1. Structure

![S3-Compatible API structure](../pictures/s3-compatible-api.png)

- S3-Compatible-API base on the original BTFS API and an extra Object-Meta module.
- The Object-Meta module is a service used to store and manage bucket/object metadata.
- BTFS will also integrate with an Access-Key module, this module used to store and manage access-keys of the S3-Compatible-API.
- S3-Compatible-API server should be a independent module and can be separately started from the BTFS daemon.

#### 2. Start S3-Compatible API server according to the corresponding configuration or startup command options.

```text
if (option.s3-compatible-api == "true" || (option.s3-compatible-api == "" && config.S3CompatibleAPI.Enable == true) {
    start_s3_compatible_api_server(config.Address.S3CompatibleAPI);
}
```

#### 3. Generate Access Key

```text
function generate_access_key() (object access_key) {
    key = random_string(key_length);
    secret = random_string(secret_length);
    access_key = {
        "key": key,
        "secret": secret,
        "enable": true,
        "created_at": now()
    }
    store_access_key(access_key);
    return access_key;
}
```

## Backwards Compatibility

True

## Test Cases

## Implementation
