## Testing Quantum's Xcellis Stornext appliance as an iRODS resource

1. Add a NAS share by creating either a SMB/CIFS or NFSv3 type share on your appliance.

    1.1 To create an SMB/CIFS type share 
    ```
    BHM:node1> share create smb test1 /stornext/snfs1/test1 write list = <userid>
    ```
    
    1.2 To create an NFSv3 type share
    ```
    BHM:node1> share create nfs test2 /stornext/snfs1/test2 
    ```
    
    1.3 Login to the NAS CLI over SSH and change permissions for the created shares
    ```
    sudo chmod 777 -R /stornext/snfs1/test2
    ```

2. Mount the created share on your iRODS system.

    2.1 Install required packages `cifs-utils` or `nfs-common`

    Ubuntu:
    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    sudo apt-get install nfs-common
    ```
    
    2.2 Create a local directory to act as a mount point
    ```
    mkdir -p /mnt/smb
    ```
    or
    ```
    mkdir -p /mnt/nfs
    ```
   
    2.3 Add entries in `/etc/fstab` to make the mounts permanent

    For the CIFS/SMB protocol:
    ```
    \\<ipaddress>\test1 /mnt/smb cifs  username=<userid>,password=<password>,noauto,rw,dir_mode=0777,file_mode=0666  0 0
    ```

    For the NFSv3 mount:
    ```
    \\<ipaddress>:/stornext/snfs1/test2 /mnt/nfs  nfs    defaults 0 0
    ```

3. Clone this repository into `/tmp` on the iRODS server
```
cd /tmp
git clone https://github.com/irods/irods_testing_quantum_xcellis
```

4. Move the test file into the iRODS test directory
```
sudo mv /tmp/irods_testing_quantum_xcellis/test_quantum_xcellis_resource.py /var/lib/irods/scripts/irods/test/
```

5. Change the owner of the test file
```
sudo chown irods:irods /var/lib/irods/scripts/irods/test/test_quantum_xcellis_resource.py
```

6. Run the test file as the `irods` service account (will take ~10 minutes)

```
sudo su - irods -c "python scripts/run_tests.py --run_specific_test test_quantum_xcellis_resource"
```
