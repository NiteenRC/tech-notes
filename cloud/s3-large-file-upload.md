# 📦 S3 Large File Uploader with Retry (Java)

This project demonstrates how to reliably upload large files to Amazon S3 using AWS SDK v1's `TransferManager`, with built-in support for multipart uploads, progress monitoring, and automatic retry logic using exponential backoff.

---

## ✅ Features

- ✅ Multipart uploads (optimized for large files > 5MB)
- 🔁 Retry with exponential backoff on failure
- 📊 Upload progress tracking
- ✅ Graceful shutdown after upload
- 🧪 Production-ready patterns and tips

---

## 🚀 Prerequisites

Ensure you have the following set up before running the uploader:

- ✅ Java 8 or higher
- ✅ Maven or Gradle for dependency management
- ✅ AWS credentials configured using:
    - `~/.aws/credentials`
    - Environment variables
    - IAM role (if running on EC2/ECS)

---

## 🧱 Maven Dependency

Add this to your `pom.xml`:

```xml
<dependency>
  <groupId>com.amazonaws</groupId>
  <artifactId>aws-java-sdk-s3</artifactId>
  <version>1.12.686</version> <!-- Use the latest compatible version -->
</dependency>
```

## 💻 Java Code
Below is the full Java implementation that includes retry logic, exponential backoff, multipart upload configuration, and graceful shutdown.

```
import com.amazonaws.AmazonClientException;
import com.amazonaws.AmazonServiceException;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.transfer.TransferManager;
import com.amazonaws.services.s3.transfer.TransferManagerBuilder;
import com.amazonaws.services.s3.transfer.Upload;

import java.io.File;

public class S3LargeFileUploader {

    private static final int MAX_RETRIES = 3;
    private static final long INITIAL_BACKOFF_MS = 2000;

    public void uploadLargeFile(File file, String keyName, String bucketName) {
        AmazonS3 s3Client = AmazonS3ClientBuilder.defaultClient();

        TransferManager tm = TransferManagerBuilder.standard()
            .withS3Client(s3Client)
            .withMultipartUploadThreshold(5L * 1024 * 1024) // 5 MB
            .withMinimumUploadPartSize(10L * 1024 * 1024)   // 10 MB
            .build();

        int attempt = 0;
        boolean success = false;

        while (attempt < MAX_RETRIES && !success) {
            try {
                System.out.printf("Attempt %d to upload '%s'%n", attempt + 1, file.getName());

                Upload upload = tm.upload(bucketName, keyName, file);
                upload.addProgressListener(progressEvent -> {
                    System.out.printf("Uploaded: %.2f MB%n",
                        upload.getProgress().getBytesTransferred() / (1024.0 * 1024.0));
                });

                upload.waitForCompletion();
                System.out.println("✅ Upload completed successfully.");
                success = true;

            } catch (AmazonServiceException | AmazonClientException | InterruptedException e) {
                attempt++;
                if (e instanceof InterruptedException) {
                    Thread.currentThread().interrupt();
                }

                System.err.printf("❌ Upload attempt %d failed: %s%n", attempt, e.getMessage());

                if (attempt < MAX_RETRIES) {
                    long backoff = INITIAL_BACKOFF_MS * (1L << (attempt - 1)); // exponential backoff
                    System.out.printf("🔁 Retrying after %d ms...%n", backoff);
                    try {
                        Thread.sleep(backoff);
                    } catch (InterruptedException ie) {
                        Thread.currentThread().interrupt();
                        break;
                    }
                } else {
                    System.err.println("⛔ Max retries reached. Upload failed.");
                }
            }
        }

        tm.shutdownNow(false);
    }

    public static void main(String[] args) {
        File file = new File("path/to/your/large-file.zip");
        String keyName = "uploads/large-file.zip";
        String bucketName = "your-s3-bucket-name";

        new S3LargeFileUploader().uploadLargeFile(file, keyName, bucketName);
    }
}
```