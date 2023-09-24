# GPU-Passthrough-LookingGlass
This is guide for Laptop with Hybrid-Graphics
# DO IT AT YOUR OWN RISK, I AM NOT RESPONSIBLE FOR ANY DAMAGE TO YOUR HARDWARE
---------------------------------------------------------------------------------------------------------------------
Hello Everyone,
Update from my project before (https://github.com/Alamputraaf/GPU-Passthrough-Nvidia).
Im currently using HDMI Dummy and Looking Glass for remote the VM.

Actually, this is my summary of installing my virtual machine, I hope you can understand it. I'm very happy with this project.

You can look full documentation on archwiki pci ovmf website (https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF)

---------------------------------------------------------------------------------------------------------------------
# Instalation
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
# Installing virt-manager.qemu,libvirt, Networking
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

# Configure the win11.xml
- We have to configure the win11.xml to insert the Graphics Cards to working flawlessly, you can look on my win11.xml (https://github.com/Alamputraaf/GPU-Passthrough-LookingGlass/blob/main/win11.xml)
- 
  
  


Thanks a lot to My Favorite Youtuber 'Mutahar' @Someordinarygamers, Reddit Community r/VFIO and many more in documentation.
