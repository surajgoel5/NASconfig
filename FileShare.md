
#  OMV Setup

First, we go to Storage>Filesystems. Here add an EXT4 Filesystem, and select the relavant disk (in my case sdb/sdc/sdd) and click save. 
This will take some time to set up the drive. 

Now


# Proxmox SMB Mount

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


# Transfer SMB Share to CTs in Proxmox
Since CTs in proxmox are usually unprivledged, the user ids start from 100000, instead of 0. 
This means even when you mount smb shares within an unprivledged CT, you cannot access it since only root can properly access it. 

So, a work around is to mount the shares in Proxmox, and then share it to individual CTs.

To make an smb available in CTs of proxmox, run the following in proxmox shell 
```
pct set CTID -mp0 /mnt/pve/SMBSHARENAME ,mp=/MOUNTING/PATH/IN/CT
```
where `CTID` is the container ID, `104` in my case. 

