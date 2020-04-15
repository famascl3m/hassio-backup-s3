# Hassio Addon for Backing up to S3 Bucket

Add-on for uploading hass.io snapshots to AWS S3.

## Installation

Save files in /addons/buckets3 on your hassio machine.

Under the Add-on Store tab in the Hass.io Supervisor view in HA add this repo as an add-on repository: https://github.com/jperquin/hassio-backup-s3.

Install, then set the config variables that you obtained from setting up the AWS account, user and bucket (see below):
awskey: `access key id`
awssecret: `secret access key`
bucketname: `AWS S3 bucket name`

Steps to setting up an Amazon AWS account:
1. Go to https://portal.aws.amazon.com/billing/signup#/start

2. Create a bucket following the standard settings (any name will do):
https://s3.console.aws.amazon.com/s3/

Note the AWS S3 Bucket name

3. Create a specific user with AmazonS3FullAccess rights
https://console.aws.amazon.com/iam

Make sure that after completing the user creation wizard you note down the Access key ID and Secret Access Key. Especially the Secret Access Key will only be displayed once..

To sync your HASSIO backup folder with AWS just click START in this add-on. It will keep a synced cloud-copy, so any purged backup files will not be kept in your bucket either. 

You could automate this using Automation:

```
# backups
- alias: Make snapshot
  trigger:
    platform: time
    at: '3:00:00'
  condition:
    condition: time
    weekday:
      - mon
  action:
    service: hassio.snapshot_full
    data_template:
      name: Automated Backup {{ now().strftime('%Y-%m-%d') }}

- alias: Upload to S3
  trigger:
    platform: time
    at: '3:30:00'
  condition:
    condition: time
    weekday:
      - mon
  action:
    service: hassio.addon_start
    data:
      addon: local_backup_s3
```

The automation above first makes a snapshot at 3am, and then at 3.30am uploads to S3.

Contact: jperquin@perquin.net

Credits: Based on rrostt/hassio-backup-s3
