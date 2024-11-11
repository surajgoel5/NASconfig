# Drives Configuration.


Using [Adaptec RAID ASR-5405Z 512MB - FH PCIe-x8](https://storage.microsemi.com/en-us/support/raid/sas_raid/sas-5405z/)

# ADaptec BIOS
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

# Proxmox

We should see the three drives now in proxmox. 
I did _Initialise using GPT_ for each drive. 
If it doesnt work, try _wipe disk_ before _Initialise with GPT_

#### Making disks available to OMV/any VM.


