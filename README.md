
# Install Amazon Linux on a Local Machine and Change the Password

This guide will walk you through the steps to install Amazon Linux on your local machine using VMware or VirtualBox, and how to change the default password using a `seed.iso` file.

<img src="https://github.com/user-attachments/assets/afab8a47-4ebb-449d-812f-6065c9074b18" alt="amazon linux 2023" width="500"/>



## Prerequisites

- VMware Workstation or VirtualBox installed on your machine.
- ISO file for Amazon Linux (download it from [Amazon Linux 2 ISO Downloads](https://cdn.amazonlinux.com/os-images/2.0.20240916.0/). 

## Steps

### 1. Download and Install Amazon Linux on a VM

1. Download the Amazon Linux 2 ISO for VMware from the [Amazon Linux 2 ISO for VMware](https://cdn.amazonlinux.com/os-images/2.0.20240916.0/vmware/). (Click on amzn2-vmware_esx-2.0.20240916.0-x86_64.xfs.gpt.ova)
2. Open your VMware
3. Create a new virtual machine and point it to the downloaded ISO as the installation media.
4. Install Amazon Linux following the prompts.

### 2. Create a Seed ISO to Change the Password

#### a. Create `meta-data` and `user-data` Files:

- **meta-data** file content (you can name the hostname as per your choice):
    ```
    instance-id: iid-local01
    local-hostname: amazon-linux
    ```

- **user-data** file content (replace `newpassword` with your desired password):
    ```
    #cloud-config
    password: newpassword
    chpasswd: { expire: False }
    ssh_pwauth: True
    ```

#### b. Generate the `seed.iso` File:

To generate the seed.iso file, run the following command:
```bash
genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
```

#### c. Attach the Seed ISO to Your VM:

1. Open your virtual machine settings.
2. Attach the `seed.iso` file as a CD/DVD drive in VMware or VirtualBox.
3. Boot your virtual machine with the `seed.iso` attached.

### 3. Log in with Your New Password

Once the virtual machine has booted, the new password you set in the `user-data` file should be applied. Log in using the `ec2-user` and the new password.

![image](https://github.com/user-attachments/assets/9ec792f5-f604-4962-8e95-466776bbb5c7)

### 4. (Optional) Remove the Seed ISO

After successfully logging in, you can remove the seed.iso file from the VM settings since it’s no longer needed.

---

## Troubleshooting

### If CD/DVD Drive Does Not Appear in VMware Settings

If the **CD/DVD drive** does not appear in the VMware settings, you can manually add it:

1. **Shut down the virtual machine** if it is running.
2. Open the **VM settings** window.
3. Click on **Add** to add new hardware.
4. Select **CD/DVD Drive** from the list of devices and click **Next**.
5. Choose **Use ISO image file** and browse to select your `seed.iso` file.
6. Click **Finish** to add the CD/DVD drive.
7. Ensure that **Connect at power on** is checked, and boot your virtual machine again.

---

## Setting Up a Shared Folder in VMware

If you want to share a folder between your host machine and the virtual machine, follow these steps:

1. **Ensure VMware Tools are Installed**: 
    - In the VMware menu, click on **VM** > **Install VMware Tools**. This will mount the VMware Tools ISO to the VM.
    - In Amazon Linux, run the following commands to install the tools:
    ```bash
    sudo yum install open-vm-tools -y
    sudo systemctl enable --now vmtoolsd
    ```

2. **Set up Shared Folder**:
    - Go to the VMware **Settings** for your VM.
    - Click on the **Options** tab.
    - Select **Shared Folders** and choose **Always enabled**.
    - Click on **Add** and browse to the folder on your host machine you want to share.
    - Check **Map as a network drive** and **Enable this share**.

![image](https://github.com/user-attachments/assets/92a876d5-1d65-48ea-9363-376f7d1b2f04)


3. **Access the Shared Folder**:
    - Inside your virtual machine, shared folders are typically mounted under `/mnt/hgfs/`. You can access them by running:
    ```bash
    cd /mnt/hgfs/
    ls
    ```
    - You should see the shared folder listed and can now use it to transfer files between your host and the VM.

  ![image](https://github.com/user-attachments/assets/aa506f18-2736-40e9-b47d-e77e716e3efb)                    ![image](https://github.com/user-attachments/assets/fb9cc270-6f02-43e4-b954-631f8963998f)



---

By following these steps, you will be able to install Amazon Linux on your local machine, change the default password, and set up a shared folder between your host and the virtual machine.

## Troubleshooting

- Make sure it is enabled:
  ```bash
    sudo systemctl restart vmtoolsd
    ```

- If you are having trouble to see shared folder on VM, try manually mount the folder again:
    ```bash
    sudo mount -t fuse.vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other
    ```

