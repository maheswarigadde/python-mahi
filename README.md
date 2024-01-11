# python-mahi hi hello how are you

import os
from azure.storage.blob import BlobServiceClient
from azure.core.exceptions import ResourceExistsError
storage_connection_string="DefaultEndpointsProtocol=https;AccountName=pythondemomahi;AccountKey=kvWimYfPTdrbFSqlhoKnn/BBzf8UKHXl5/DnGoa2CUS09w9jNlxPelk4ZotXpR+ChE2EglySASw5+AStMwAz4w==;EndpointSuffix=core.windows.net"
Blob_service_client=BlobServiceClient.from_connection_string(storage_connection_string)
# create container

container_id=r'python-output'
container_client=Blob_service_client.create_container(container_id)
print(container_client)
# create local folder and upload to azure
local_path=r"C:\Users\mahes\Downloads\sampledemomahi"
os.makedirs(local_path, exist_ok=True)
local_file_name=r"mahidemo" +".txt"
upload_file_path=os.path.join(local_path,local_file_name)
file=open(file=upload_file_path,mode="w")
file.write("hello,mahi")
file.close
blob_client=Blob_service_client.get_blob_client(container='python-input',blob=local_file_name)
print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)
with open(file=upload_file_path, mode="rb") as data:
    blob_client.upload_blob(data, overwrite=True)
    print("upload_blob")
# move file from one folder to another
	source_container_id='python-input'
source_blob_name='mahidemo'
destination_container_id='python-output'
destination_blob_name='mahidemo'
source_blob_client = Blob_service_client.get_blob_client(container=source_container_id, blob=source_blob_name)
destination_blob_client = Blob_service_client.get_blob_client(container=destination_container_id, blob=destination_blob_name)
blob_data = source_blob_client.download_blob()
destination_blob_client.upload_blob(blob_data.readall())
print(f"Blob '{source_blob_name}' moved from '{source_container_id}' to '{destination_container_id}'.")

 # list the containers   
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
# download the file from azure
	download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
container_client = Blob_service_client.get_container_client(container= container_id) 
print("\nDownloading blob to \n\t" + download_file_path)

with open(file=download_file_path, mode="wb") as download_file:
 download_file.write(container_client.download_blob(blob.name).readall())
 
 # delete the container
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)
os.rmdir(local_path)

print("Done")