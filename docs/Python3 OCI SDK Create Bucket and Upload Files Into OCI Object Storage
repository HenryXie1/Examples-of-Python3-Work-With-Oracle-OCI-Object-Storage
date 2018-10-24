# Python3 OCI SDK Create Bucket and Upload Files Into OCI Object Storage

###  Requirement:

We need to use OCI object storage for our backup purpose.  First to create Bucket, then we put all our backup files into the bucket.  
Before we do that, we need to setup config file for OCI SDK to get correct user credential, tenancy, compartment_id ...etc. Refer [my blog for example][1]:

###  Solution:

####  Create bucket example:
```
#!/u01/python3/bin/python3  
import oci  
import argparse  
#set bhucket name to create  
parser = argparse.ArgumentParser(description= 'Create Bucket in Oracle cloud Object Storage')  
parser.add_argument('bucketname',help='The name of bucket to be created')  
parser.add_argument('--version', '-v', action='version', version='%(prog)s 1.0')  
args = parser.parse_args()  
bucket_name = args.bucketname  
config = oci.config.from_file()  
identity = oci.identity.IdentityClient(config)  
compartment_id = config["compartment_id"]  
object_storage = oci.object_storage.ObjectStorageClient(config)  
namespace = object_storage.get_namespace().data  
from oci.object_storage.models import CreateBucketDetails  
request = CreateBucketDetails()  
request.compartment_id = compartment_id  
request.name = bucket_name  
bucket = object_storage.create_bucket(namespace, request)  
print(f'Create bucket "{bucket_name}" in compartment_id "{compartment_id}')
```
####  Upload files into the bucket example:
```
#!/u01/python3/bin/python3  
import oci  
import glob  
import os  
import argparse  
parser = argparse.ArgumentParser(description= 'Upload files into Oracle cloud Object Storage')  
parser.add_argument('bucketname',help='The name of bucket to upload')  
parser.add_argument('files',nargs='*',help='The Full path files to upload, wildcard can be used to match multiple files, ie *livesql-archivelog*')  
parser.add_argument('--version', '-v', action='version', version='%(prog)s 1.0')  
args = parser.parse_args()  
mybucketname = args.bucketname  
upload_files_loc = args.files  
config = oci.config.from_file()  
identity = oci.identity.IdentityClient(config)  
compartment_id = config["compartment_id"]  
object_storage = oci.object_storage.ObjectStorageClient(config)  
namespace = object_storage.get_namespace().data  
for path in upload_files_loc:  
    with open(path,'rb') as f:  
       obj = object_storage.put_object(namespace,mybucketname,os.path.basename(path),f)  
       print(f'uploaded "{path}" to bucket "{mybucketname}"')
```
####  Make the Script executable without python intepeter
pip install pyinstaller
pyinstaller -F  < your python script>
in dist folder, you will see the executable file of your python script.
remember it needs  ~/.oci/config and ~/.oci/oci api key , these 2 file to login oracle OCI. 
otherwise you may get error like:

_oci.exceptions.ConfigFileNotFound: Could not find config file at /root/.oci/config_

[1]: http://www.henryxieblogs.com/2018/10/prepare-config-file-for-python3-oci-sdk.html
