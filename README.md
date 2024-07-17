Here's a tailored for my HP ZBook Studio G3 that utilizes the Intel HD Graphics 530 for display and the NVIDIA Quadro M2000M for compute on Rocky Linux 8:


 **Install Necessary Drivers:**
   - Ensure that you have the appropriate drivers for both GPUs installed on your system.

1. **Install the Intel OpenCL Runtime:**
   - You need to install the Intel OpenCL runtime to enable OpenCL support on the Intel GPU.

   Add the repository and install the runtime:
   ```bash
   sudo tee /etc/yum.repos.d/intel-opencl.repo <<EOF
   [intel-opencl]
   name=Intel(R) Graphics Compute Runtime for OpenCL(TM)
   baseurl=https://repositories.intel.com/graphics/rpm/rhel8
   enabled=1
   gpgcheck=1
   gpgkey=https://repositories.intel.com/graphics/intel-graphics.key
   EOF

   sudo dnf install intel-opencl-clang intel-ocloc intel-opencl
   ```



2. **Install the DKU:**
   - Download the appropriate DKU version from the Autodesk website.
     https://www.autodesk.com/support/technical/article/caas/sfdcarticles/sfdcarticles/CentOS-ISO-installer-and-Linux-Driver-Kernel-Utilities-for-Flame-Family.html

Download the correct DKU from the system requirements web page, for example: Flame System Requirements and extract the contents of the file, for example: tar -xvf <DKU_file_name>.tar.
In the directory where the DKU was extracted, run the following to install the DKU: ./INSTALL_DKU. For usage, type INSTALL_DKU --help.
If a Wacom tablet or storage devices were disconnected or turned off as part of the OS installation, reconnect and power them up.
Restart the workstation with reboot.



1. **Create or Edit Xorg Configuration File:**
   Create or edit the Xorg configuration file to configure both GPUs. 

### 1. `/etc/X11/xorg.conf.d/10-intel.conf`

```bash
sudo nano /etc/X11/xorg.conf.d/10-intel.conf
```

Add the following content:

```plaintext
Section "Device"
    Identifier "IntelGPU"
    Driver "intel"
    BusID "PCI:0:2:0"
    Option "AccelMethod" "sna"
    Option "TearFree" "true"
    Option "DRI" "3"
EndSection

Section "Screen"
    Identifier "ScreenIntel"
    Device "IntelGPU"
    Monitor "Monitor0"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1080"
    EndSubSection
EndSection

Section "Monitor"
    Identifier "Monitor0"
    Option "DPMS"
EndSection
```

### 2. `/etc/X11/xorg.conf.d/20-nvidia.conf`

```bash
sudo nano /etc/X11/xorg.conf.d/20-nvidia.conf
```

Add the following content:

```plaintext
Section "Device"
    Identifier "NvidiaGPU"
    Driver "nvidia"
    BusID "PCI:1:0:0"
    Option "AllowEmptyInitialConfiguration"
    Option "Coolbits" "28"
EndSection

Section "Screen"
    Identifier "ScreenNvidia"
    Device "NvidiaGPU"
EndSection

Section "Module"
    Load "glx"
EndSection

Section "Files"
    ModulePath "/usr/lib64/nvidia/xorg"
    ModulePath "/usr/lib64/xorg/modules"
EndSection
```

### 3. `/etc/X11/xorg.conf.d/30-layout.conf`

```bash
sudo nano /etc/X11/xorg.conf.d/30-layout.conf
```

Add the following content:

```plaintext
Section "ServerLayout"
    Identifier "layout"
    Screen 0 "ScreenIntel"
    Inactive "ScreenNvidia"
EndSection
```

### Applying and Verifying Configuration

1. **Save and Close the Files:**
   - Save each file after editing by pressing `Ctrl+X`, then `Y`, and `Enter`.

2. **Reboot the System:**
   ```bash
   sudo reboot
   ```

3. **Verify Configuration:**

This setup should ensure that Autodesk Flame and other applications can utilize the Intel HD Graphics 530 for display and the NVIDIA Quadro M2000M for compute tasks on Rocky Linux 8.

   **Autodesk Flame:**
   - Ensure that Flame is configured to use the Intel GPU for specific tasks. This may involve modifying configuration files or setting environment variables as specified in the Flame documentation.

   **Autodesk Maya:**
   - Maya can use multiple GPUs for different tasks. Ensure that the Intel GPU is available and recognized by Maya.

5. **Balancing Load Between GPUs:**
   - You can use the Intel GPU for less intensive tasks or as a secondary compute device. For example, you might configure rendering tasks to use the NVIDIA GPU while using the Intel GPU for other computations.

   **Environment Variable:**
   ```bash
   export GPU_DEVICE=Intel # This is just an example; actual configuration may vary
   ```

6. **Reboot and Verify:**
   - Reboot your system to ensure that all changes take effect.

   ```bash
   sudo reboot
   ```

   - Use `clinfo` again to verify that both GPUs are available and correctly configured.


By following these steps, you should have the Intel HD Graphics 530 configured for display and the NVIDIA Quadro M2000M configured for compute tasks on Rocky Linux 8. This setup allows you to utilize both GPUs effectively for different purposes.












