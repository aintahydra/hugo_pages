---
title: "Run the REMnux VMs on a Apple Silicon (M2) Macbook"
date: 2023-01-05T07:26:12
draft: false
tags: [remnux,apple,OSX,M2,UTM]
---

# References

1. https://www.technomancer.com/archives/568
2. https://www.youtube.com/watch?v=krGvTMq29gA

## Convert the format of the REMNux disk image
`$ qemu-img convert -f vmdk -O vhdx REMnuxVM.vmdk REMnuxVM.vhdx`
    * (According to [1], the REMnux VMWare disk image (.vmdk) is converted to the VHDX format whereas [2] changes the image to the QCOM2 format; `qemu-img convert -p -O qcom2 Remnux-disk1.vmdk Remnux.qcom2`)

## Import the converted disk image

1. run UTM
2. on the Welcome windows, choose "Create a New Virtual Machine"
3. on the Start window, choose the slow `:(` "Emulate" (not "Virtualize")
4. on the Operating System window, choose "Other" (not "Windows" nor "Linux")
5. on the Other window, 
    - uncheck "Skip ISO boot"
    - press the "Browse" button, choose the converted image as the Boot ISO Image
6. on the "Hardware", leave the options like below
    - Architecture: x86_64,
    - System: Standard PC(Q35 + ICH9, 2009) (alias of pc-q35-6.2) q35
    - Memory: 4G
    - CPU Cores: Default
7. 
- Emulate
- Windows
- Check, "Import VHDX Image", Choose Boot Image as "REMnuxVM.vmx", and Continue
- on "Hardware" Window, Just continue (Architecture: "x86_64", System: Standard PC(q35), Mem 4G, CPU Default)
- and Wait for a while (in my case 50mins)



UTM - create - Emulate

Windows / Linux / Other 중, Other 선택
옵션:
(v) Skip ISO boot
  (Next)


  (Next)

Strorage 1GB???

Shared Directory(nothing)

((After a VM is created))

Option -> QEMU
 - uncheck UEFI boot
 - (ring device 만 체크됨)

Option -> IDE Drive
  -> 빨간거 누름
  -> 왼쪽 아래 New Device 선택
  -> IDE Drive 가 선택되어 있는 상태에서 "Import"
  -> Remnux.qcow2 (아까 만든 HDD 이미지)
  -> 한참 작업하다가 마지막엔 Devices 목록에 IDE Drive 생성됨

해당 IDE Drive를 선택하고 "Save"