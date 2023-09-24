# GPU-Passthrough-LookingGlass
This is guide for Laptop with Hybrid-Graphics
# DO IT AT YOUR OWN RISK, I AM NOT RESPONSIBLE FOR ANY DAMAGE TO YOUR HARDWARE

Hello Everyone,
Update from my project before (https://github.com/Alamputraaf/GPU-Passthrough-Nvidia).
Im currently using HDMI Dummy and Looking Glass for remote the VM.
---------------------------------------------------------------------------------------------------------------------
# Preparation
- Make sure your processor is supported Virtualization (AMD or Intel).
- Enable IOMMU on your Linux host. You can edit on your kernel parameter.
- Make sure the Graphics Cards are in their own IOMMU, not seperated (THIS IS IMPORTANT).
- Make sure IOMMU are enabled.
* IOMMU checking
  ```sh
  dmesg | grep -i -e DMAR -e IOMMU
  ```
- Find Your Graphics Cards IDs
  ```sh
  lspci -nnk
  ```
- 
- 



Thanks a lot to My Favorite Youtuber 'Mutahar' @Someordinarygamers, Reddit Community r/VFIO and many more in documentation.
