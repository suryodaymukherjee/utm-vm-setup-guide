# Ubuntu VM Setup Guide for macOS

This guide provides step-by-step instructions for setting up an Ubuntu Virtual Machine (VM) on macOS using UTM. It also includes instructions for mounting a shared folder between macOS and the VM, and configuring a static IP address for the VM using a bridged network.

## Prerequisites

1. **macOS System**: Ensure you are using a macOS system.
2. **UTM Application**: Download and install UTM from the [official website](https://mac.getutm.app/).
3. **Ubuntu ISO File**: Download the Ubuntu ISO file from the [official Ubuntu website](https://ubuntu.com/download/desktop).

## Steps to Set Up Ubuntu VM on macOS

### 1. Install UTM
- Download the UTM application from the [official website](https://mac.getutm.app/).
- Open the `.dmg` file and drag the UTM app to your Applications folder.

### 2. Create a New Virtual Machine

1. **Open UTM**: Launch the UTM application from your Applications folder.

2. **Create a New VM**:
   - Click on the `+` button in the top-right corner of the UTM interface.
   - Select `Virtualize` (for ARM-based systems) or `Emulate` (for x86-based systems).
   - Choose `Linux` as the operating system type.

3. **Upload the Ubuntu ISO**:
   - In the `Boot ISO Image` section, click `Browse` and select the Ubuntu ISO file you downloaded earlier.

4. **Configure VM Settings**:
   - **Memory**: Allocate at least 2 GB of RAM (4 GB recommended for better performance).
   - **CPU Cores**: Assign 2 or more CPU cores, depending on your macOS system's capacity.
   - **Storage**: Create a virtual disk with at least 20 GB of space.

5. **Network Configuration**:
   - Set the network mode to `Bridged` for better connectivity and to enable static IP configuration later.

6. **Finalize and Start the VM**:
   - Click `Save` to finalize the VM configuration.
   - Select the newly created VM and click `Play` to start it.

7. **Install Ubuntu**:
   - Follow the on-screen instructions to install Ubuntu.
   - During installation, choose the default options unless you have specific requirements.

### 3. Mount a Shared Folder

To share files between macOS and the Ubuntu VM, you can use one of the following methods: Virtio-FS or SPICE WebDAV. Both methods allow seamless file sharing between the host and the VM.

#### Method 1: Virtio-FS

Virtio-FS is a high-performance file-sharing mechanism that allows direct access to a shared directory on the host system.

1. **Enable Virtio-FS in UTM**:
   - Open UTM and select your Ubuntu VM.
   - Click on the `Edit` button to access the VM settings.
   - Navigate to the `Sharing` tab.
   - Enable the `Virtio-FS` option and click the `+` button to add a folder.
   - Select the folder on macOS that you want to share with the VM.

2. **Install Required Packages in Ubuntu**:
   - Start the Ubuntu VM and open a terminal.
   - Update the package list:
     ```bash
     sudo apt update
     ```
   - Install the `virtiofsd` package:
     ```bash
     sudo apt install virtiofsd
     ```

3. **Mount the Shared Folder in Ubuntu**:
   - Create a mount point for the shared folder:
     ```bash
     sudo mkdir /mnt/shared
     ```
   - Mount the shared folder using the `virtiofs` driver:
     ```bash
     sudo mount -t virtiofs shared /mnt/shared
     ```
   - Verify the folder's contents:
     ```bash
     ls /mnt/shared
     ```

4. **Automate Mounting (Optional)**:
   - Add the following entry to `/etc/fstab` to mount the folder automatically on boot:
     ```
     shared /mnt/shared virtiofs defaults 0 0
     ```

#### Method 2: SPICE WebDAV

SPICE WebDAV allows mounting WebDAV shares as filesystems, enabling seamless file sharing between macOS and the Ubuntu VM.

1. **Enable SPICE WebDAV in UTM**:
   - Open UTM and select your Ubuntu VM.
   - Click on the `Edit` button to access the VM settings.
   - Navigate to the `Sharing` tab.
   - Enable the `SPICE WebDAV` option and click the `+` button to add a folder.
   - Select the folder on macOS that you want to share with the VM.

2. **Install Required Packages**:
   - Update the package list:
     ```bash
     sudo apt update
     ```
   - Install the necessary packages:
     ```bash
     sudo apt install spice-vdagent spice-webdavd davfs2
     ```

3. **Create a Mount Point**:
   - Create a directory to serve as the mount point for the shared folder:
     ```bash
     mkdir -p ~/shared
     ```

4. **Start the WebDAV Daemon**:
   - Enable the SPICE WebDAV service to start on boot:
     ```bash
     sudo systemctl enable spice-webdavd
     ```
   - Start the service immediately:
     ```bash
     sudo systemctl start spice-webdavd
     ```

5. **Reboot the VM**:
   - Reboot the VM to apply the changes:
     ```bash
     sudo reboot
     ```

6. **Mount the WebDAV Share**:
   - Mount the WebDAV share to the created mount point:
     ```bash
     sudo mount -t davfs http://localhost:9843 ~/shared
     ```

7. **Optional: Automount on Boot**:
   - To mount the WebDAV share automatically on boot, add the following line to `/etc/fstab`:
     ```
     http://localhost:9843 /home/yourusername/shared davfs user,noauto 0 0
     ```
   - Allow your user to mount it without `sudo`:
     ```bash
     sudo usermod -aG davfs2 yourusername
     ```

8. **Troubleshooting Tips**:
   - If you encounter issues, ensure the shared folder is correctly configured in UTM.
   - Verify the tag used for Virtio-FS (it should be `share`).
   - Check if the SPICE WebDAV server is running:
     ```bash
     sudo systemctl status spice-webdavd
     ```
   - Use `dmesg` to check for errors:
     ```bash
     sudo dmesg | tail
     ```
   - Ensure the required packages are installed and up-to-date:
     ```bash
     sudo apt update
     sudo apt upgrade
     ```

### 4. Configure a Static IP Address
To set up a static IP address for the VM using a bridged network:
1. In UTM, configure the network settings to use `Bridged Networking`.
2. Inside the Ubuntu VM, edit the Netplan configuration file:
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```
3. Add the following configuration (replace with your network details):
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       eth0:
         dhcp4: no
         addresses:
           - 192.168.1.100/24
         gateway4: 192.168.1.1
         nameservers:
           addresses:
             - 8.8.8.8
             - 8.8.4.4
   ```
4. Apply the changes:
   ```bash
   sudo netplan apply
   ```

## Additional Resources
- [UTM Documentation](https://docs.getutm.app/)
- [Ubuntu Documentation](https://help.ubuntu.com/)

## Notes
- Ensure your macOS and UTM versions are up-to-date.
- Use the latest stable version of Ubuntu for the best experience.

## Contributing
Contributions are welcome! Please submit a pull request or open an issue for any suggestions or improvements.

## License
This guide is licensed under the MIT License.