# Taking backup file and store it to aws S3 using Python
You can use the following python codes to task the backup file of apache documentroot. It create a backup tar files and copy to the AWS s3 bucket. For this task we used boto3,tarfile,posixpath and os modules.
#### Prerequisite:
1. Install python
2. Create a programamtic user with neccessary policies.

#### Script
```
import os
import posixpath
import tarfile
import datetime
import boto3
from botocore.client import Config

ACCESS_KEY_ID = ''
ACCESS_SECRET_KEY = ''
BUCKET_NAME = 'apachebackup001'

documentRoot = "apache-docroot/"

for siteDir in os.listdir(documentRoot):
  
  absPath = posixpath.join(documentRoot,siteDir)
    
  t = datetime.datetime.now()
  ts = '{}-{}-{}-{}-{}'.format(t.day,t.month,t.year,t.hour,t.minute,)
    
  tarName = '/tmp/{}-{}.tar.gz'.format(siteDir,ts)
  
  print("=> Creating TarFile {}".format(tarName))  

  tar = tarfile.open(tarName,'w:gz')
    
  tar.add(absPath)
  
  tar.close()
  
  print("=> Uploading TarFile {}".format(tarName))
  
  data = open(tarName, 'rb')

  s3 = boto3.resource(
    's3',
    aws_access_key_id=ACCESS_KEY_ID,
    aws_secret_access_key=ACCESS_SECRET_KEY,
    config=Config(signature_version='s3v4')
  )
  s3.Bucket(BUCKET_NAME).put_object(Key=tarName, Body=data)

  os.remove(tarName) 
```
#### output
=> Creating TarFile /tmp/www.yahoo.com-27-1-2021-11-44.tar.gz
=> Uploading TarFile /tmp/www.yahoo.com-27-1-2021-11-44.tar.gz
=> Creating TarFile /tmp/www.google.com-27-1-2021-11-44.tar.gz
=> Uploading TarFile /tmp/www.google.com-27-1-2021-11-44.tar.gz
=> Creating TarFile /tmp/download.kernel.org-27-1-2021-11-44.tar.gz
=> Uploading TarFile /tmp/download.kernel.org-27-1-2021-11-44.tar.gz
=> Creating TarFile /tmp/www.fsf.org-27-1-2021-11-44.tar.gz
=> Uploading TarFile /tmp/www.fsf.org-27-1-2021-11-44.tar.gz
