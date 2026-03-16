```java
@Override
public String upload(String bucketName,
                     String key,
                     InputStream in,
                     long contentLength,
                     String contentType,
                     boolean publicRead) {

  if (!amazonS3.doesBucketExistV2(bucketName)) {
    createBucket(bucketName);
  }

  ObjectMetadata metadata = new ObjectMetadata();
  if (contentLength >= 0) metadata.setContentLength(contentLength);
  if (contentType != null && !contentType.isBlank()) metadata.setContentType(contentType);

  PutObjectRequest req = new PutObjectRequest(bucketName, key, in, metadata);

  if (publicRead) {
    req.withCannedAcl(CannedAccessControlList.PublicRead);
  }

  amazonS3.putObject(req);

  return amazonS3.getUrl(bucketName, key).toString();
}

@Override
  public String uploadFileToBucketPath(String bucketName, String folderName, String fileName, String pacContent) {
  String key = normalizeKey(folderName, fileName); // "pac/" + "xxx.pac" 형태로 안전하게
  byte[] bytes = pacContent.getBytes(StandardCharsets.UTF_8);

  return upload(bucketName,
          key,
          new ByteArrayInputStream(bytes),
          bytes.length,
          "text/plain; charset=utf-8",
          true);
  }

private String normalizeKey(String folderName, String fileName) {
  String f = folderName == null ? "" : folderName.trim();
  if (!f.isEmpty() && !f.endsWith("/")) f += "/";
  return f + fileName;
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