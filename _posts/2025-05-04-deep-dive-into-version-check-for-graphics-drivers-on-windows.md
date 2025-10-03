---
layout: post
title: Deep Dive into Version Check for Graphics Drivers on Windows
date: 2025-05-04 13:06 -0400
categories: [DisableVersionMismatchCheck]
tags: [Reverse Engineering]
---



Sometimes, we experience a graphics driver failure (Code 31) that indicates the device is not working properly when being loaded after we replace the old kernel binary named amdkmdag.sys with a newly rebuilt one for step debugging on the targeted Windows, if we forget to import the registry setting below into the system simultaneously.



Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\GraphicsDrivers]
"DisableVersionMismatchCheck"=dword:00000001

![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-54-56.png)
_Yellow Bang on AMD Graphics Driver in Device Manager_





How come? Let's deeply dive into it. As we know, dxgkrnl.sys plays as the fundamental infrastructure for DirectX graphics kernel subsystem. It's responsible for loading and running display miniport drivers, e.g. AMD graphics drivers.



There's an exported symbol function named DpiFdoValidateKmdAndPnpVersionMatch in the dxgkrnl module, which is called from dxgkrnl!DpiFdoInitializeFdo().

Actually, the function DpiFdoValidateKmdAndPnpVersionMatch() will be called after amdkmdag!DriverEntry() and amdkmdag!AtiAddDevice().



Child-SP          	RetAddr               Call Site

00 ffffd508`15d61f98 fffff800`1db81337     dxgkrnl!DpiFdoValidateKmdAndPnpVersionMatch [onecoreuap\windows\core\dxkernel\dxgkrnl\port\dpfdo.cxx @ 7786]

01 ffffd508`15d61fa0 fffff800`1db808fb     dxgkrnl!DpiFdoInitializeFdo+0x313 [onecoreuap\windows\core\dxkernel\dxgkrnl\port\dpfdo.cxx @ 8167]

02 ffffd508`15d621b0 fffff800`1097bdbf     dxgkrnl!DpiAddDevice+0x195b [onecoreuap\windows\core\dxkernel\dxgkrnl\port\dpport.cxx @ 1830]

03 ffffd508`15d626d0 fffff800`10d579f0     nt!PpvUtilCallAddDevice+0x3b

04 ffffd508`15d62710 fffff800`10d6caef     nt!PnpCallAddDevice+0x94

05 ffffd508`15d627d0 fffff800`10d6be77     nt!PipCallDriverAddDevice+0x827

06 ffffd508`15d62990 fffff800`10d30706     nt!PipProcessDevNodeTree+0x333

07 ffffd508`15d62a60 fffff800`10981d4a     nt!PiRestartDevice+0xba

08 ffffd508`15d62ab0 fffff800`10850545     nt!PnpDeviceActionWorker+0x46a

09 ffffd508`15d62b70 fffff800`1090e6f5     nt!ExpWorkerThread+0x105

0a ffffd508`15d62c10 fffff800`10a05878     nt!PspSystemThreadStartup+0x55

0b ffffd508`15d62c60 00000000`00000000     nt!KiStartSystemThread+0x28



When disassembling the function dxgkrnl!DpiFdoInitializeFdo(), we can see there's a string named 'DisableVersionMismatchCheck' involved in this call, which has the same name as we import the workaround to the system at the beginning of this article.



![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-55-32.png)
![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-55-43.png)




In the function DpiFdoValidateKmdAndPnpVersionMatch(), dxgkrnl module compares the  PNP version, which is grabbed from the related INF file, with the kernel binary file version. That's called version check. If they don't match each other, dxgkrnl will break into the debugger if it's present, and outputs the string ''A mismatch between the PNP/INF version and the KMD file version on the graphics adapter has been detected. The adapter will fail to start." Furthermore, the function call returns a NTSTATUS error 0xc0000182, which is explained as "The I/O device is configured incorrectly or the configuration parameters to the driver are incorrect." It corroborates the device status report when the registry key DisableVersionMismatchCheck is absent from the system.


![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-55-56.png)
![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-56-6.png)
![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-56-21.png)












That's the way this function works. If we don't want to import the registry key DisableVersionMismatchCheck, but we still would like the rebuilt binary works as we expected, the only path is to sync the file version of the amdkmdag.sys to the PNP version, before getting the binary signed by Osibuild.

We can leverage the DDR tool to collect the correct PNP version from the system. As we see, the current version is 31.0.12012.4006.

![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-56-28.png)





The file version of kernel binary amdkmdag.sys can be altered by any third party resource editor, e.g. RisohEditor, which is able to be found at https://github.com/katahiromz/RisohEditor

Originally, we can see the file version that RisoEditor reads from the binary, which is produced by MS C/C++ compiler.

![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-57-0.png)




After aligning the file version to the PNP version and recompiling, we finally get a revised binary file,  same as the way the WHQL or NonWHQL building works.


![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-58-4.png)
![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-58-17.png)




Eventually, the kernel binary amdkmdag.sys works without the registry key DisableVersionMismatchCheck as we expected.

![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-58-33.png)
![alt text](/assets/images/deep-dive-into-version-check/image-2023-10-5_14-58-43.png)











