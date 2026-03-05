```java
@Override
  public String uploadFileToBucketPath(String bucketName, String folderName, String fileName, String pacContent) {
    if (!amazonS3.doesBucketExistV2(bucketName)) {
      createBucket(bucketName);
    }

    ListObjectsRequest listObjectsRequest = new ListObjectsRequest()
            .withBucketName(bucketName)
            .withDelimiter("/")
            .withMaxKeys(300);
    ObjectListing objectListing = amazonS3.listObjects(listObjectsRequest);
    boolean isExist = false;
    for (String commonPrefixes : objectListing.getCommonPrefixes()) {
      if(StringUtils.hasText(commonPrefixes) && commonPrefixes.contains("pac")) {
        isExist = true;
        break;
      }
    }

    if(!isExist) {
      ObjectMetadata objectMetadata = new ObjectMetadata();
      objectMetadata.setContentLength(0L);
      objectMetadata.setContentType("application/x-directory");
      PutObjectRequest putObjectRequest = new PutObjectRequest(bucketName, folderName, new ByteArrayInputStream(new byte[0]), objectMetadata);
      amazonS3.putObject(putObjectRequest);
      setObjectACL(bucketName, folderName);
    }

    InputStream inputStream = new ByteArrayInputStream(pacContent.getBytes());
    ObjectMetadata metadata = new ObjectMetadata();
    String filePath = folderName + fileName;
    PutObjectRequest request = new PutObjectRequest(bucketName, filePath, inputStream, metadata);
    amazonS3.putObject(request);
    setObjectACL(bucketName, filePath);

    return amazonS3.getUrl(bucketName, filePath).toString();
  }
```

```java
 String pacUrl;
 String bucketName = applicationConfig.getPacStorage().getBucketName() + "-" + dbConnectInfo.getCustNo().toLowerCase();
    String folderName = "pac/";
    pacUrl = cloudObjectStorageClient.uploadFileToBucketPath(
            bucketName,
            folderName,
            customerSystem.getCustSysNm() + ".pac",
            pacScript
    );
```