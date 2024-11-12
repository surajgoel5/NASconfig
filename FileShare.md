
#  OMV Setup

First, we go to Storage>Filesystems. Here add an EXT4 Filesystem, and select the relavant disk (in my case sdb/sdc/sdd) and click save. 
This will take some time to set up the drive. 
Right after this, you will be taken to the mounting the filesystem page. If you accidentally miss this, mount the filesystem manually to OMV Storage>Filesystems>Mount (triangle button).

Now that we have the filesystem mounted, we can now creat a shared folder in Storage>Shared Folder. In my case, I created a shared folder called `\photos`.

Now we share the folder in reality by using SMB.
In Services>SMB/CIFS , enable the service with relavant options, follwed by sharing the newly created shared folder. 

# Proxmox SMB Mount FOR LXC CONTAINER

To mount this SMB share in proxmox, we run the following command

```
sudo mount -t cifs -o username=OMV_USERNAME,uid=100000,gid=100000 //OMV_IP/SHARED_FOLDER_NAME /mnt/pve/PROXMOX_MOUNTED_FOLDER_NAME/
```

Here, `OMV_USERNAME` is the username of the user we are trying to access OMV through. Make sure you have made this user in OMV.
`OMV_IP` is the ip address of the OMV server, and `SHARED_FOLDER_NAME` is the corresponding hsared folder name.
'PROXMOX_MOUNTED_FOLDER_NAME' is the name you choose to call the mounted folder. 

We have chosen `uid` and `gid` carefully as they correspond to the root user of an LXC Container. Therefore, we can actually edit the files from within the LXC.
To make shis shared folder available in CTs of proxmox, run the following in proxmox shell 
```
pct set CTID -mp0 /mnt/pve/PROXMOX_MOUNTED_FOLDER_NAME,mp=/MOUNTING/PATH/IN/CT
```
where `CTID` is the container ID, `104` in my case. 





# The following method did not work.
#### Proxmox SMB Mount

Now we need to mound these SMB Shares on proxmox. 
This can be easily done using GUI-

Datacenter>Storage>Add>SMB/CIFS

Now fill up the form- 
1. id= any folder name (e.g. photos-smb)
2. server: ip address of omv
3. username: user to access shares. Make sure you make this user in OMV.
4. pass:
5. share: folder name from OMV

After this goes through, the smb share should be mounted in /mnt/pve in proxmox. 

similar commands to mount SMB shares are available for any linux VM from cmdline


#### Transfer SMB Share to CTs in Proxmox
Since CTs in proxmox are usually unprivledged, the user ids start from 100000, instead of 0. 
This means even when you mount smb shares within an unprivledged CT, you cannot access it since only root can properly access it. 

So, a work around is to mount the shares in Proxmox, and then share it to individual CTs.

To make an smb available in CTs of proxmox, run the following in proxmox shell 
```
pct set CTID -mp0 /mnt/pve/SMBSHARENAME ,mp=/MOUNTING/PATH/IN/CT
```
where `CTID` is the container ID, `104` in my case. 

