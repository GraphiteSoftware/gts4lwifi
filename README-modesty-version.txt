23 Aug 2019, jmuir
------------------

This branch originates from the 0.99.27 release (May 2019) of the
Modesty kernel.

However, the current version of Modesty is 0.99.33 (July 2019).

We use 0.99.27 to avoid a device-encryption defect we see when 0.99.33
is used with a generic-system-image (GSI).  This defect is not present
when 0.99.33 is used with a factory system-image.

When 0.99.33 is used with a GSI, the tablet boots and you can get
adb-shell access, but it is not able to encrypt the userdata partition
because it cannot communicate with qseecom (logs below).  There must
be some components from the factory system-image that are needed to
support the communication with qseecomd.

  $ grep -i qseecom last_kmsg

  QseecomStartApp (bksecapp) call success status=0, AppId=131074
  <6>[    0.000000]  [0:        swapper:    0] Reserved memory: reserved region for node 'qseecom_region': base 0x00000000fb200000, size 36 MiB
  <6>[    0.781407]  [4:      swapper/0:    1] QSEECOM: qseecom_probe: qseecom.qsee_version = 0x1000000
  <4>[    0.781615]  [4:      swapper/0:    1] QSEECOM: qseecom_retrieve_ce_data: Device does not support PFE
  <6>[    0.781812]  [4:      swapper/0:    1] QSEECOM: qseecom_probe: fde_key_size=1
  <6>[    0.781966]  [4:      swapper/0:    1] QSEECOM: qseecom_probe: no-clock-support=0x1
  <4>[    0.782126]  [4:      swapper/0:    1] QSEECOM: qseecom_probe: qsee reentrancy support phase is not defined, setting to default 0
  <4>[    0.782594]  [4:      swapper/0:    1] QSEECOM: qseecom_probe: qseecom.whitelist_support = 1
  <14>[   8.789908]  [4:           init:    1] init: starting service 'qseecomd'...
  <6>[    8.844204]  [5:       qseecomd:  546] capability: warning: `qseecomd' uses 32-bit capabilities (legacy support in use)
  <3>[    8.868857]  [4:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    8.893758]  [4:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    8.896346]  [4:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    8.899487]  [4:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    8.900228]  [4:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    8.901355]  [7:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <4>[    8.902274]  [7:android.hardwar:  548] QSEECOM: qseecom_load_app: App (skeymast) does'nt exist, loading apps for first time
  <4>[    9.010383]  [4:android.hardwar:  548] QSEECOM: qseecom_load_app: App with id 196611 (skeymast) now loaded
  <3>[    9.010679]  [6:       qseecomd:  565] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    9.013454]  [6:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <3>[    9.015177]  [6:       qseecomd:  553] scm_call failed: func id 0x72000206, ret: -1, syscall returns: 0x0, 0x0, 0x0
  <14>[   9.384806]  [3:           init:    1] init: processing action (sys.qseecomd.enable=true) from (/init.rc:1997)
  <14>[   9.386446]  [3:           init:    1] init: Command 'start tbaseLoader' action=sys.qseecomd.enable=true (/init.rc:1998) returned 0 took 1ms.
  <14>[   9.386683]  [3:           init:    1] init: Command 'start insthk' action=sys.qseecomd.enable=true (/init.rc:1999) returned -1 took 0ms.
  <14>[   9.386853]  [3:           init:    1] init: Command 'start dhkprov1x' action=sys.qseecomd.enable=true (/init.rc:2000) returned -1 took 0ms.
  <14>[   9.387020]  [3:           init:    1] init: Command 'start dhkprov2x' action=sys.qseecomd.enable=true (/init.rc:2001) returned -1 took 0ms.
  <14>[   9.387209]  [3:           init:    1] init: Command 'start bsd' action=sys.qseecomd.enable=true (/init.rc:2002) returned -1 took 0ms.
  <4>[   10.889511]  [5:vendor.samsung.:  621] QSEECOM: qseecom_unload_app: App id 262148 now unloaded
  <4>[   13.289232]  [7:vendor.samsung.:  621] QSEECOM: qseecom_unload_app: App id 327684 now unloaded
  <4>[   14.739436]  [0:vendor.samsung.:  621] QSEECOM: qseecom_unload_app: App id 393220 now unloaded
  <4>[   15.530564]  [2:vendor.samsung.:  621] QSEECOM: qseecom_unload_app: App id 458756 now unloaded
