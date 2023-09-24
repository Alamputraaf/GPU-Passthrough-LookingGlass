# GPU-Passthrough-LookingGlass
This is guide for Laptop with Hybrid-Graphics
# DO IT AT YOUR OWN RISK, I AM NOT RESPONSIBLE FOR ANY DAMAGE TO YOUR HARDWARE

Hello Everyone,
Update from my project before (https://github.com/Alamputraaf/GPU-Passthrough-Nvidia).
Im currently using HDMI Dummy and Looking Glass for remote the VM.
---------------------------------------------------------------------------------------------------------------------
# Instalation
- Make sure your processor is supported Virtualization (AMD or Intel).
- Enable IOMMU on your Linux host. You can edit on your kernel parameter, Save it and reboot the system
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
- 


Thanks a lot to My Favorite Youtuber 'Mutahar' @Someordinarygamers, Reddit Community r/VFIO and many more in documentation.
