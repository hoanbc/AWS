#### 1) Create KMS Key
```yml
KMS -> Customer managed keys -> Create key -> Key type: Symmetric -> Key usage: Encrypt and decrypt -> Alias: KMS-for-S3-Notification -> Key policy: Fill as below
{
    "Version": "2012-10-17",
    "Id": "KMS-for-S3-Notification",
    "Statement": [
        {
            "Sid": "Allow access for Root User",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::457043140818:root"
            },
            "Action": "kms:*",
            "Resource": "*"
        },
        {
            "Sid": "Allow access for Key User (S3 Service Principal)",
            "Effect": "Allow",
            "Principal": {
                "Service": "s3.amazonaws.com"
            },
            "Action": [
                "kms:GenerateDataKey*",
                "kms:Decrypt"
            ],
            "Resource": "*"
        }
    ]
}
-> Next (Done)
```

#### 2) Create SNS Topic
```yml
Amazon SNS -> Topics -> Create topic -> Standard -> Name: SNS-for-S3-Notification -> Encryption: Enable -> AWS KMS key: KMS-for-S3-Notification -> Access Policy: Fill as below
{
  "Version": "2012-10-17",
  "Id": "SNS-for-S3-Notification",
  "Statement": [
    {
      "Sid": "Alow S3 Notification Publish to SNS",
      "Effect": "Allow",
      "Principal": {
        "Service": "s3.amazonaws.com"
      },
      "Action": "SNS:Publish",
      "Resource": "arn:aws:sns:ap-southeast-1:457043140818:testvn-project-sns",
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": "457043140818"
        },
        "ArnLike": {
          "aws:SourceArn": "arn:aws:s3:::hbc-demo1"
        }
      }
    }
  ]
}
-> Next (Done)
```

#### 3) Create S3 Notification
```yml
Amazon S3 -> Buckets -> hbc-demo1 -> Event notifications -> Create event notification -> Event name: Send-Alert-to-SNS -> Event types: Object creation or All object removal events -> Destination: SNS topic -> Choose from your SNS topics: SNS-for-S3-Notification -> Next (Done)
```
