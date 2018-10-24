# Python3 OCI SDK Download And Delete Files From OCI Object Storage

### Requirement:
We need to use OCI object storage for our backup purpose.  We need to download backup files , also  we need to  delete obsolete backup files.
Before we do that, we need to setup config file for OCI SDK to get correct user credential, tenancy, compartment_id ...etc. Refer Refer [my blog for example][1]:

###  Solution:

####  Download files example:
```
#!/u01/python3/bin/python3
import oci
import argparse
parser = argparse.ArgumentParser(description= 'Download files from Oracle cloud Object Storage')
parser.add_argument('bucketname',help='The name of bucket to download from ')
parser.add_argument('files_location',help='The full path of location to save downloaded files, ie  /u01/archivelogs')
parser.add_argument('prefix_files',nargs='*',help='The filenames to download, No wildcard needed, ie livesql will match livesql*')
parser.add_argument('--version', '-v', action='version', version='%(prog)s 1.0')
args = parser.parse_args()
mybucketname = args.bucketname
retrieve_files_loc = args.files_location
prefix_files_name = args.prefix_files
print(args)
config = oci.config.from_file()
identity = oci.identity.IdentityClient(config)
compartment_id = config["compartment_id"]
object_storage = oci.object_storage.ObjectStorageClient(config)
namespace = object_storage.get_namespace().data
listfiles = object_storage.list_objects(namespace,mybucketname,prefix=prefix_files_name)
#print(listfiles.data.objects)
for filenames in listfiles.data.objects:
   get_obj = object_storage.get_object(namespace, mybucketname,filenames.name)
   with open(retrieve_files_loc+'/'+filenames.name,'wb') as f:
       for chunk in get_obj.data.raw.stream(1024 * 1024, decode_content=False):
           f.write(chunk)
   print(f'downloaded "{filenames.name}" in "{retrieve_files_loc}" from bucket "{mybucketname}"')
```
#### Delete files example:
```
#!/u01/python3/bin/python3
import oci
import sys
import argparse
parser = argparse.ArgumentParser(description= 'Delete files from Oracle cloud Object Storage')
parser.add_argument('bucketname',help='The name of bucket to delete from ')
parser.add_argument('prefix_files',nargs='*',help='The filenames to delete, No wildcard needed, ie livesql will match livesql*')
parser.add_argument('--version', '-v', action='version', version='%(prog)s 1.0')
args = parser.parse_args()
mybucketname = args.bucketname
prefix_files_name = args.prefix_files

config = oci.config.from_file()
identity = oci.identity.IdentityClient(config)
compartment_id = config["compartment_id"]
object_storage = oci.object_storage.ObjectStorageClient(config)
namespace = object_storage.get_namespace().data
listfiles = object_storage.list_objects(namespace,mybucketname,prefix=prefix_files_name)
#print(listfiles.data.objects)
#bool(listfiles.data.objects)
if not listfiles.data.objects:
   print('No files found to be deleted')
   sys.exit()
else:
   for filenames in listfiles.data.objects:
      print(f'File in Bucket "{mybucketname}" to be deleted: "{filenames.name}"')

deleteconfirm = input('Are you sure to delete above files? answer y or n :')
if deleteconfirm.lower() == 'y':
    for filenames in listfiles.data.objects:
        object_storage.delete_object(namespace, mybucketname,filenames.name)
        print(f'deleted "{filenames.name}" from bucket "{mybucketname}"')
else:
    print('Nothing deleted')
```
####  Make the Script executable without python intepeter
pip install pyinstaller
pyinstaller -F  < your python script> .
In dist folder, you will see the executable file of your python script.
remember it needs  ~/.oci/config and ~/.oci/oci api key , these 2 file to login oracle OCI. 
otherwise you may get error like:
_oci.exceptions.ConfigFileNotFound: Could not find config file at /root/.oci/config_

[1]: http://www.henryxieblogs.com/2018/10/prepare-config-file-for-python3-oci-sdk.html
