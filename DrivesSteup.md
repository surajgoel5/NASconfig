# Drives Configuration.


Using [Adaptec RAID ASR-5405Z 512MB - FH PCIe-x8](https://storage.microsemi.com/en-us/support/raid/sas_raid/sas-5405z/)

# Adaptec BIOS
Attach drive one by one so that you can label them physically. 

### Making individual volume:

Each drive is connected one by one. In adaptec bios, for each drive-
  1. Initilalize drive
  2. Create array using the single drive
  3. array type:  **volume**
  4. array name: some name e.g. **SAS1**
  5.  read cache: **enabled**
  6.  write cache: **disabled**
  7.  setup: quick init
accept and reboot

I had 3 drives, each 4TB, and labelled them SAS1, SAS2 and SAS3
# Proxmox

We should see the three drives now in proxmox. 

In my case, they were labelled **sda**, **sdb** and **sdc** for SAS1, SAS2 and SAS3 respectively. 

I did _Initialise using GPT_ for each drive. 
If it doesnt work, try _wipe disk_ before _Initialise with GPT_

#### Making disks available to OMV/any VM.

Check the VM's hardware in proxmox to see what SCSI devices are available and used. 
In my case Hard Disk (scsi0) was in use. This imples scsi1, scsi2, etc. are available. 

I follow [this answer](https://forum.proxmox.com/threads/how-to-properly-add-a-new-disk-and-assign-it-to-a-vm.72396/)

We go to proxmox shell and run - 
 ```
 ls -l /dev/disk/by-id
```
This command will list all the disks with their ids. We are looking for ids for each disk (corresponding to sda,sdb,sdc..)

For me, the id for sda looked like **scsi-XXXXXX**.

Now to set the disk to VM using the command
```
qm set VMID --scsi1 /dev/disk/by-id/scsi-XXXXXX
```
replace `VMID` with correspinding VM if of the VM, in my case that is `102`.

replace `--scsi1` with the corresponding scsi id you wanna put the disk in. This may change depending on available scsi ids that we checked earlier for the VM.

Now **reboot** / **start** the VM.

Now the drives should be mounted in the new VM.


