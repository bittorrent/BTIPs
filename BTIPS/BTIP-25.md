
```btip: 25
title: S3-Compatible API service
author: Steve Zhang<steve.zhang@tron.network>
discussions-to: https://github.com/bittorrent/BTIPs/issues/25
status: Draft
type: Core Protocol
category (*only required for Core Protocol): S3-Compatible API
created: 2023-07-06
```

## Simple Summary

Add an AWS S3-Compatible API service on BTFS protocol implementation.

## Abstract

In the implementation of BTFS protocol, we integrate an adapter, which provides HTTP API service, converts the received request in S3 format into BTFS native request for calling, and then converts the obtained native response into S3 format response and return the response, so as to achieve compatibility with the S3 interface protocol.

## Motivation

As a second-generation centralized cloud storage solution, AWS S3 has been widely used, and a large number of applications are based on its API protocol or SDK access. As a third-generation decentralized storage protocol, BTFS currently has a relatively narrow application range. If an application wants to access BTFS, it needs to redesign and develop an interface protocol based on BTFS. If the BTFS service itself can provide a set of AWS S3-compatible interface services, then these applications can access BTFS at a small cost, or can directly use BTFS as an optional transparent storage layer.

## Specification

### Configuration and start command options

#### 1. Add a new S3CompatibleAPI configuration field under the configuration file
```json
   {
     ...
     "S3CompatibleAPI": {
       "Enable": false,
       "Address": "127.0.0.1:5201",
       "HTTPHeaders": {
         "Access-Control-Allow-Headers": [
           "X-Requested-With",
           "Range",
           "User-Agent"
         ],
         "Access-Control-Allow-Methods": [
           "GET"
         ],
         "Access-Control-Allow-Origin": [
           "*"
         ]
       }
     },
     ...
   }
```

- Enable: Used to configure the enabled state of S3-compatibleAPI, the priority is lower than the daemon startup command option
- Address: It is used to configure the service address of the S3-compatible API. Note that when the address is configured as a non-local address, it may also be accessed externally
- HTTPHeaders: Used to configure the response headers of S3-compatible API services, which can be used to configure cross-domain rules

#### 2. Add a string type option 's3-compatible-api' to the startup command to specify whether to enable the S3API service. 

```shell
  btfs daemon --s3-compatible-api=enable
```

When it is "enable", it means that it is started, when it is "disable", it means that it is not started. The default is "", which means that it is enabled by the S3CompatibleAPI.EnableS3 in the configuration

### Access Keys related commands. 

Access Keys is used for S3 interface access authentication. Each Access Key record consists of key, secret, root, enable, and created_at fields. 
- key: the key of the key
- secret: key
- root: The root path of the key used in the BTFS mfs system, consisting of "/ + random directory name", each Access Key can only create a Bucket under its corresponding root
- enable: indicates whether to enable the Access Key, the default is true, when the value is false, the request corresponding to the Access Key will be invalid
- created_at: the creation time of the Access Key record

#### 1. Generate Access Key
```shell
btfs s3-keys generate
{"key":"xxx","secret":"xxx","root":"/xxx","enable":true,"created_at":"xxxx-xx-xx xx:xx:xx"}
```

#### 2. Disable Access Key

```shell
btfs s3-keys disable <key>
```

#### 3. List all Access Keys

```shell
btfs s3-keys list
[{"key":"xxx","secret":"xxx","root":"/xxx","enable":true,"created_at":"xxxx-xx-xx xx:xx:xx"},{"key":"xxx","secret":"xxx","root":"/xxx","enable":true,"created_at":"xxxx-xx-xx xx:xx:xx"}]
```

#### 4. Delete Access Key

```shell
btfs s3-keys delete <key>
```

#### 5. Reset Access Key

```shell
btfs s3-keys reset <key>
{"key":"xxx","secret":"xxx","root":"/xxx","enable":true,"created_at":"xxxx-xx-xx xx:xx:xx"}
```

Note: this operation will only reset the secret associated with the key.

### Authentication

The BTFS S3-compatible API only supports [AWS v4 signatures (AWS4-HMAC-SHA256)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) for authentication and does not support AWS v2 signatures at this time.

### Access Control Lists (ACLs)

- The BTFS S3-compatible API features limited support for Access Control Lists (ACLs). Object-level ACLs are currently not supported. The GetObjectAcl and GetBucketAcl methods will work as expected, but the GetObjectAcl response will return the ACL of the bucket that the object is contained in.
- The owner of the bucket is the Access Key when the Bucket was created (modification is currently not supported). Only the owner of the Bucket can modify the ACL of the Bucket and delete the Bucket.
- Supported predefined ACLs are: private, public-read, public-read-write, for detailed definitions see: AWS ACL, the default permission is public-read.
- __private__: Only the bucket owner can upload, delete, and read objects under the bucket, and read the object list under the bucket.
- __public-read__: Only the bucket owner can upload and delete objects under the bucket, but public users (including anonymous users) can read objects and read the object list under the bucket.
- __public-read-write__: public users (including anonymous users) can upload, delete, read objects under the bucket, and read the list of objects under the bucket.

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

You can Get ipfs hash from object metadata.
Object metadata key:

```json
{
   "Metadata": {
      "btfs-hash": "btfs cid"
   }
}
```

## Rationale

#### 1. Compatible adapter: convert the received S3 standard request into BTFS native API request and execute it, then convert the BTFS native API response into S3 standard response and return

![S3-Compatible API structure](../pictures/s3-compatible-api.png)

- Start the S3-Compatible API server when start the BTFS daemon.
- Accept S3 standard request.
- Check the request authentication.
- Convert S3 standard request to a BTFS original request.
- Do BTFS original request, and gain the BTFS original response.
- Convert BTFS original response to S3 standard response.
- Return the S3 standard response.

```text
function handle_s3_request(s3_request) {
    ok = check_s3_request_auth(s3_request);
    if (!ok) {
        return auth_failed_error;
    }
    original_request = convert_s3_request_to_original_request(s3_request);
    original_response = do_original_request(original_request);
    s3_response = convert_original_response_to_s3_response(original_response);
    return s3_response;
}
```

#### 2. Start S3-Compatible API server according to the corresponding configuration or startup command options.

```text
if (option.s3-compatible-api == "enalbe" || (option.s3-compatible-api == "" && config.S3CompatibleAPI.Enable == true) {
    start_s3_compatible_api_server(config.Address.S3CompatibleAPI);
}
```

#### 3. Generate Access Key

```text
function generate_access_key() {
    key = random_string(key_length);
    secret = random_string(secret_length);
    ok = false;
    while(!ok) {
        root = path_join("/", random_dir_name());
        ok = btfs_files_mkdir(root);
    }
    access_key = {
        "key": key,
        "secret": secret,
        "root": root,
        "enable": true,
        "created_at": now()
    }
    store_access_key(access_key);
    return access_key;
}
```

#### 4. Create bucket

```text
function convert_s3_create_bucket_request_to_original_files_mkdir_request(s3_create_bucket_request) {
    access_key = get_access_key(s3_create_bucket_request.key);
    original_files_mkdir_request.path = join_path(access_key.root, create_bucket_request.bucket_name);
    return original_files_mkdir_request;
}
     
function handle_s3_create_bucket_request(s3_create_bucket_request) {
    ok = check_s3_request_auth(s3_create_bucket_request);
    if (!ok) {
        return auth_failed_error;
    }
    original_files_mkdir_request = convert_s3_create_bucket_request_to_original_files_mkdir_request(s3_create_bucket_request);
    original_files_mkdir_response = do_original_files_mkdir_request(original_files_mkdir_request);
    bucket_info = {
        "owner": access_key.key,
        "bucke_name": create_bucket_request.bucket_name,
        "path": original_files_mkdir_request.path,
        "cid": original_files_mkdir_response.cid,
        "acl": create_bucket_request.acl,
        "created_at": now()
    }
    store_bucket_info(bucket_info);
    return
}
```

## Backwards Compatibility

True

## Test Cases

## Implementation
