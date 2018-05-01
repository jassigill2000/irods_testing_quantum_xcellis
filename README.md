In order to test Quantum’s StorNext - Xcellis Storage.

1.) Add a NAS share by creating either a SMB/CIFS or NFSv3 type share on your appliance
```
    1.1) To create an SMB/CIFS type share 
    BHM:node1> share create smb test1 /stornext/snfs1/test1 write list = <userid>
 
    1.2) To create an NFSv3 type share 
    BHM:node1> share create nfs test2 /stornext/snfs1/test2 

    1.3) Login to the NAS CLI over SSH and change permissions for the shares created e.g. 
    sudo chmod 777 -R /stornext/snfs1/test2/
```

2.) Now that you have created the shares you can mount them on your iRODS systems. Make sure the required NFS and SMB client tools are present on your systems.

    For Ubuntu:
    
    2.1) Install nfs-common and cifs-utils if not installed
         sudo apt-get update
         sudo apt-get install nfs-common cifs-utils
         
    2.2) Create local directories for example /mnt/smb and /mnt/nfs to act as the mount point of the shares created in step 1.
   
    2.3) To make the mounts permanent add entries in /etc/fstab file
         For the nfs mount add something like this:
         <ipaddress>:/stornext/snfs1/test2 /mnt/nfs  nfs    defaults 0 0
         
         For the CIFS/SMB protocol add something like this:
         \\<ipaddress>\test1 /mnt/smb cifs  username=<userid>,password=<password>,noauto,rw,dir_mode=0777,file_mode=0666  0 0
    
3.) On the iRODS server git clone https://github.com/irods/irods_testing_quantum_xcellis.git in the /tmp directory

4.) Move the test_quantum_xcellis_resource.py file into /var/lib/irods/scripts/irods/test/ directory
```
sudo mv /tmp/irods_testing_quantum_xcellis/test_quantum_xcellis_resource.py /var/lib/irods/scripts/irods/test/.
```
5.) Change the owner of the moved file
```
sudo chown irods:irods /var/lib/irods/scripts/irods/test/test_quantum_xcellis_resource.py
```
6.) To run the tests become the irods users sudo su - irods
And type at the prompt
```
python scripts/run_tests.py --run_s test_quantum_xcellis_resource.Test_Resource_Unixfilesystem
```
