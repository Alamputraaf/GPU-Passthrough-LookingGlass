# GPU-Passthrough-LookingGlass
- This is guide for Laptop with Mux Graphics
- Im using Arch Linux, Manjaro users can work as well.
# DO IT AT YOUR OWN RISK, I AM NOT RESPONSIBLE FOR ANY DAMAGE TO YOUR HARDWARE
---------------------------------------------------------------------------------------------------------------------
Hello Everyone,
Update from my project before (https://github.com/Alamputraaf/GPU-Passthrough-Nvidia).
Im currently using HDMI Dummy and Looking Glass for remote the VM.

Actually, this is my summary of installing my virtual machine, I hope you can understand it. I'm very happy with this project.

You can look full documentation on archwiki pci ovmf website (https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF)

---------------------------------------------------------------------------------------------------------------------
# Installation
- Make sure your processor is supported Virtualization (AMD or Intel).
- Enable IOMMU on your Linux host. You can edit on your kernel parameter, Save it and reboot the system.
- Add intel_iommu=on or amd_iommu=on depending on the processor you have. In my case i have amd processor.
- ```sh
  sudo nano /etc/default/grub
  ```
  ```sh
  # GRUB boot loader configuration

  GRUB_DEFAULT=0
  GRUB_TIMEOUT=5
  GRUB_DISTRIBUTOR="Arch"
  GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on  resume=UUID=1ec5de3e-b9b9-4cf5-80a4-dd6361a6efda loglevel=3 audit=0"
  GRUB_CMDLINE_LINUX=""
  ```
- Save the configuration and run a command
  ```sh
  sudo grub-mkconfig -o /boot/grub/grub.cfg 
  ```
- Make sure the Graphics Cards are in their own IOMMU, not seperated (THIS IS IMPORTANT).
- Make sure IOMMU are enabled.
* IOMMU checking
  ```sh
  dmesg | grep -i -e DMAR -e IOMMU
  [    0.384963] iommu: Default domain type: Translated
  [    0.384963] iommu: DMA domain TLB invalidation policy: lazy mode
  [    0.465137] pci 0000:00:00.2: AMD-Vi: IOMMU performance counters supported
  [    0.465201] pci 0000:00:01.0: Adding to iommu group 0
  [    0.465219] pci 0000:00:01.1: Adding to iommu group 1
  [    0.465237] pci 0000:00:01.2: Adding to iommu group 2
  [    0.465261] pci 0000:00:02.0: Adding to iommu group 3

  ```
- Find Your Graphics Cards IDs
  ```sh
  lspci -nnk
  ```
- Note your Graphics Cards IDs (For Example my IDs are [10de:1f99] and [10de:10fa])
  ```sh
  01:00.0 VGA compatible controller [0300]: NVIDIA Corporation TU117M [10de:1f99] (rev a1)
        Subsystem: Lenovo TU117M [17aa:3a43]
        Kernel driver in use: NVIDIA
        Kernel modules: nouveau, nvidia_drm, nvidia
  01:00.1 Audio device [0403]: NVIDIA Corporation Device [10de:10fa] (rev a1)
        Subsystem: NVIDIA Corporation Device [10de:10fa]
        Kernel driver in use: NVIDIA
        Kernel modules: snd_hda_intel

  ```
- Add your Graphics Cards IDs to directory vfio
  ```sh
  sudo nano /etc/modprobe.d/vfio.conf
  ```
  ```sh
  options vfio-pci ids=10de:1f99,10de:10fa
  ```
- Recompile initramfs and reboot the system
  ```sh
  sudo mkinitcpio -p linux
  ```
- Make sure your Graphics Cards Kernel Driver using vfio-pci
  ```sh
  lspci -nnk
  ```
  ```sh
  01:00.0 VGA compatible controller [0300]: NVIDIA Corporation TU117M [10de:1f99] (rev a1)
        Subsystem: Lenovo TU117M [17aa:3a43]
        Kernel driver in use: vfio-pci
        Kernel modules: nouveau, nvidia_drm, nvidia
  01:00.1 Audio device [0403]: NVIDIA Corporation Device [10de:10fa] (rev a1)
        Subsystem: NVIDIA Corporation Device [10de:10fa]
        Kernel driver in use: vfio-pci
        Kernel modules: snd_hda_intel

  ```
  - And we done setup on vfio modules
# Installing Virt-Manager, Qemu, Libvirt, and Networking 
- We need to install virt-manager,qemu,libvirt, and network for the virtual machines,let's install
  ```sh
  sudo pacman -S  qemu-desktop libvirt edk2-ovmf virt-manager dnsmasq
  ```
- First we Enable and start the libvirt service and second is logging for virtlogd by typing a command
  ```sh
  sudo systemctl enable libvirtd.service
  ```
  ```sh
  sudo systemctl start libvirtd.service
  ```
  ```sh
  sudo systemctl enable virtlogd.socket
  ```
  ```sh
  sudo systemctl start virtlogd.socket
  ```
- After that we have to activate the default libvirt network by typing command
  ```sh
  sudo virsh net-autostart default
  ```
  ```sh
  sudo virsh net-start default
  ```

  ---------------------------------------------------------------------------------------------------------------------
# Preparing The Virtual Machines on virt-manager
- First we need to install a Windows VM
<div align="center">
  <a href="images/virt-manager.png">
    <img src="https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/images/virt-manager.png" alt="virt-manager" width="640" height="640">
  </a>
</div>

- Click create a Virtual Machines.
<div align="center">
  <a href="images/virt-manager-2.png">
    <img src="https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/images/virt-manager-2.png" alt="virt-manager" width="640" height="640">
  </a>
</div>

- Choose your iso file, for instance im installing Windows 11, you can go whatever you want.
<div align="center">
  <a href="images/virt-manager-3.png">
    <img src="https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/images/virt-manager-3.png" alt="virt-manager" width="640" height="640">
  </a>
</div>

- After that, you can give the CPUs and The RAM for Windows VM, For example I have 16 GB RAM, I want to give 8GB and 8 CPUs
- Remember, the CPUs is may be different for you, i have 6 cores with 12 threads.
- I want to give 8 CPUs to Windows 11 and the rest 4 Cores for the Linux.
- You can check your total CPUs by typing a command
  ```sh
  lscpu -e
  CPU NODE SOCKET CORE L1d:L1i:L2:L3 ONLINE    MAXMHZ    MINMHZ       MHZ
  0    0      0    0 0:0:0:0          yes 3000.0000 1400.0000 1473.9919
  1    0      0    0 0:0:0:0          yes 3000.0000 1400.0000 3000.0000
  2    0      0    1 1:1:1:0          yes 3000.0000 1400.0000 3000.0000
  3    0      0    1 1:1:1:0          yes 3000.0000 1400.0000 1397.4919
  4    0      0    2 2:2:2:0          yes 3000.0000 1400.0000 3000.0000
  5    0      0    2 2:2:2:0          yes 3000.0000 1400.0000 3000.0000
  6    0      0    3 4:4:4:1          yes 3000.0000 1400.0000 3000.0000
  7    0      0    3 4:4:4:1          yes 3000.0000 1400.0000 3000.0000
  8    0      0    4 5:5:5:1          yes 3000.0000 1400.0000 1559.7889
  9    0      0    4 5:5:5:1          yes 3000.0000 1400.0000 3000.0000
   10    0      0    5 6:6:6:1          yes 3000.0000 1400.0000 3000.0000
   11    0      0    5 6:6:6:1          yes 3000.0000 1400.0000 3000.0000

  ```
  <div align="center">
  <a href="images/virt-manager-4.png">
    <img src="https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/images/virt-manager-4.png" alt="virt-manager" width="640" height="640">
  </a>
</div>

- Give Storage to VM, im giving 25 G for example
  <div align="center">
  <a href="images/virt-manager-5.png">
    <img src="https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/images/virt-manager-5.png" alt="virt-manager" width="640" height="640">
  </a>
</div>
