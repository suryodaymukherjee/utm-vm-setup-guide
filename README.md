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

### Screenshots for Reference

#### UTM Main Interface
![UTM Main Interface](https://mac.getutm.app/images/screenshots/utm-main.png)

#### VM Configuration
![VM Configuration](https://mac.getutm.app/images/screenshots/utm-config.png)

#### Ubuntu Installation
![Ubuntu Installation](https://ubuntu.com/tutorials/install-ubuntu-desktop/_files/ubuntu-installation.png)

These screenshots are sourced from the official UTM and Ubuntu websites for better clarity.

### 3. Mount a Shared Folder
To share files between macOS and the Ubuntu VM:
1. In UTM, go to the VM settings and enable the `Shared Directory` option.
2. Specify the folder on macOS that you want to share.
3. Inside the Ubuntu VM, install the `spice-vdagent` package:
   ```bash
   sudo apt update
   sudo apt install spice-vdagent
   ```
4. Restart the VM and access the shared folder under `/media` or `/mnt`.

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