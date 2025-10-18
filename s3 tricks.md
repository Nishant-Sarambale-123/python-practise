Perfect 🔥 — you want a **complete list of all major S3 operations using Python (boto3)** — including **create, list, upload, download, delete, permissions, versioning**, etc.

Let’s go step by step.
You can **copy-paste** these examples directly into your Python environment (with AWS credentials configured).

---

# 🪣 **All Major S3 Operations using boto3**

> Import boto3 and create your S3 resource/client first 👇

```python
import boto3

# High-level resource (easier for object operations)
s3 = boto3.resource('s3')

# Low-level client (for advanced operations)
s3_client = boto3.client('s3')
```

---

## 🔹 1️⃣ List All Buckets

```python
for bucket in s3.buckets.all():
    print(bucket.name)
```

or

```python
response = s3_client.list_buckets()
for bucket in response['Buckets']:
    print(bucket['Name'])
```

---

## 🔹 2️⃣ Create a Bucket

```python
bucket_name = "nishant-demo-bucket"
s3.create_bucket(Bucket=bucket_name)
print("Bucket created:", bucket_name)
```

✅ **Note:**
In some regions (other than `us-east-1`), you must include `CreateBucketConfiguration`:

```python
s3.create_bucket(
    Bucket=bucket_name,
    CreateBucketConfiguration={'LocationConstraint': 'ap-south-1'}
)
```

---

## 🔹 3️⃣ Upload a File

```python
file_name = "sample.txt"
bucket_name = "nishant-demo-bucket"

s3.Bucket(bucket_name).upload_file(file_name, "uploaded_sample.txt")
print("File uploaded successfully!")
```

---

## 🔹 4️⃣ Download a File

```python
s3.Bucket(bucket_name).download_file("uploaded_sample.txt", "downloaded_sample.txt")
print("File downloaded successfully!")
```

---

## 🔹 5️⃣ List Files (Objects) inside a Bucket

```python
for obj in s3.Bucket(bucket_name).objects.all():
    print(obj.key)
```

---

## 🔹 6️⃣ Delete a File (Object)

```python
s3.Object(bucket_name, "uploaded_sample.txt").delete()
print("File deleted!")
```

---

## 🔹 7️⃣ Delete a Bucket

> ⚠️ You must delete **all files inside** before deleting the bucket.

```python
bucket = s3.Bucket(bucket_name)

# First delete all objects
bucket.objects.all().delete()

# Then delete the bucket
bucket.delete()

print("Bucket deleted!")
```

---

## 🔹 8️⃣ Check if a Bucket Exists

```python
all_buckets = [bucket.name for bucket in s3.buckets.all()]
bucket_name = "nishant-demo-bucket"

if bucket_name in all_buckets:
    print("Bucket exists")
else:
    print("Bucket does not exist")
```

---

## 🔹 9️⃣ Copy File Between Buckets

```python
copy_source = {
    'Bucket': 'source-bucket',
    'Key': 'file.txt'
}
s3.Bucket('destination-bucket').copy(copy_source, 'copied_file.txt')
print("File copied successfully!")
```

---

## 🔹 🔟 Enable Versioning on a Bucket

```python
bucket_versioning = s3.BucketVersioning(bucket_name)
bucket_versioning.enable()
print("Versioning enabled!")
```

---

## 🔹 11️⃣ Get Bucket Versioning Status

```python
status = s3.BucketVersioning(bucket_name).status
print("Versioning status:", status)
```

---

## 🔹 12️⃣ Make Object Public

```python
s3.ObjectAcl(bucket_name, 'uploaded_sample.txt').put(ACL='public-read')
print("Object made public!")
```

---

## 🔹 13️⃣ Generate a Pre-Signed URL (Temporary Access)

```python
url = s3_client.generate_presigned_url(
    'get_object',
    Params={'Bucket': bucket_name, 'Key': 'uploaded_sample.txt'},
    ExpiresIn=3600  # 1 hour
)
print("Pre-signed URL:", url)
```

---

## 🔹 14️⃣ Get Bucket Policy

```python
response = s3_client.get_bucket_policy(Bucket=bucket_name)
print(response['Policy'])
```

---

## 🔹 15️⃣ Set Bucket Policy

```python
policy = {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": f"arn:aws:s3:::{bucket_name}/*"
        }
    ]
}

import json
s3_client.put_bucket_policy(Bucket=bucket_name, Policy=json.dumps(policy))
print("Bucket policy applied!")
```

---

## 🔹 16️⃣ Enable Server-Side Encryption (SSE-S3)

```python
s3_client.put_bucket_encryption(
    Bucket=bucket_name,
    ServerSideEncryptionConfiguration={
        'Rules': [{
            'ApplyServerSideEncryptionByDefault': {'SSEAlgorithm': 'AES256'}
        }]
    }
)
print("Encryption enabled!")
```

---

## 🔹 17️⃣ Get Bucket Encryption Status

```python
encryption = s3_client.get_bucket_encryption(Bucket=bucket_name)
print(encryption)
```

---

## 🔹 18️⃣ Enable Logging on a Bucket

```python
s3_client.put_bucket_logging(
    Bucket=bucket_name,
    BucketLoggingStatus={
        'LoggingEnabled': {
            'TargetBucket': 'my-log-bucket',
            'TargetPrefix': 'log/'
        }
    }
)
print("Logging enabled!")
```

---

## 🔹 19️⃣ Tag a Bucket

```python
s3_client.put_bucket_tagging(
    Bucket=bucket_name,
    Tagging={'TagSet': [{'Key': 'Environment', 'Value': 'DevOps'}]}
)
print("Bucket tagged!")
```

---

## 🔹 20️⃣ Get Bucket Location

```python
location = s3_client.get_bucket_location(Bucket=bucket_name)
print("Bucket region:", location['LocationConstraint'])
```

---

## 🧩 Summary — Easy Way to Remember

| Operation         | boto3 Command                                   |
| ----------------- | ----------------------------------------------- |
| List buckets      | `s3.buckets.all()`                              |
| Create bucket     | `s3.create_bucket()`                            |
| Upload file       | `bucket.upload_file()`                          |
| Download file     | `bucket.download_file()`                        |
| Delete file       | `object.delete()`                               |
| Delete bucket     | `bucket.delete()`                               |
| Enable versioning | `BucketVersioning(bucket).enable()`             |
| Public object     | `ObjectAcl(bucket, key).put(ACL='public-read')` |
| Pre-signed URL    | `client.generate_presigned_url()`               |
| Encryption        | `put_bucket_encryption()`                       |

🧠 **Pattern to remember:**

> Resource → For looping and object actions
> Client → For configuration or API-level actions

---

Would you like me to make a **one-page “S3 boto3 commands cheat sheet PDF”** — with short examples and patterns to memorize before interviews?
