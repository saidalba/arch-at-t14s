### sys-info
```bash
$ sudo dmidecode -s system-version
ThinkPad T14s Gen 6
```

```bash
$ hostnamectl
  Static hostname: <redacted>
        Icon name: computer-laptop
          Chassis: laptop 💻
Chassis Asset Tag: No Asset Information
       Machine ID: <redacted>
          Boot ID: <redacted>
 Operating System: Arch Linux                  
           Kernel: Linux 7.1.3-arch2-2
     Architecture: x86-64
  Hardware Vendor: Lenovo
   Hardware Model: ThinkPad T14s Gen 6
     Hardware SKU: LENOVO_MT_21R2_BU_Think_FM_ThinkPad T14s Gen 6
 Firmware Version: N4KET18W (1.09 )
    Firmware Date: Wed 2025-10-22
     Firmware Age: 8month 3w 5d
```

#### hardware-specs

```bash
$ lscpu
Architecture:                x86_64
  CPU op-mode(s):            32-bit, 64-bit
  Address sizes:             46 bits physical, 48 bits virtual
  Byte Order:                Little Endian
CPU(s):                      14
  On-line CPU(s) list:       0-13
Vendor ID:                   GenuineIntel
  Model name:                Intel(R) Core(TM) Ultra 7 255U
    CPU family:              6
    Model:                   181
    Thread(s) per core:      2
    Core(s) per socket:      12
    Socket(s):               1
    Stepping:                0
    Microcode version:       0xd
    CPU(s) scaling MHz:      22%
    CPU max MHz:             5200.0000
    CPU min MHz:             400.0000
    BogoMIPS:                6528.00
    Flags:                   fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca 
                             cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht t
                             m pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_
                             perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpui
                             d aperfmperf tsc_known_freq pni pclmulqdq dtes64 monitor 
                             ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid
                              sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes
                              xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fa
                             ult epb ssbd ibrs ibpb stibp ibrs_enhanced tpr_shadow fle
                             xpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 s
                             mep bmi2 erms invpcid rdseed adx smap clflushopt clwb int
                             el_pt sha_ni xsaveopt xsavec xgetbv1 xsaves split_lock_de
                             tect user_shstk avx_vnni dtherm ida arat pln pts hwp hwp_
                             notify hwp_act_window hwp_epp hwp_pkg_req hfi vnmi umip p
                             ku ospke waitpkg gfni vaes vpclmulqdq rdpid bus_lock_dete
                             ct movdiri movdir64b fsrm md_clear serialize arch_lbr ibt
                              flush_l1d arch_capabilities
Virtualization features:     
  Virtualization:            VT-x
Caches (sum of all):         
  L1d:                       352 KiB (10 instances)
  L1i:                       640 KiB (10 instances)
  L2:                        10 MiB (5 instances)
  L3:                        12 MiB (1 instance)
NUMA:                        
  NUMA node(s):              1
  NUMA node0 CPU(s):         0-13
Vulnerabilities:             
  Gather data sampling:      Not affected
  Ghostwrite:                Not affected
  Indirect target selection: Not affected
  Itlb multihit:             Not affected
  L1tf:                      Not affected
  Mds:                       Not affected
  Meltdown:                  Not affected
  Mmio stale data:           Not affected
  Old microcode:             Not affected
  Reg file data sampling:    Not affected
  Retbleed:                  Not affected
  Spec rstack overflow:      Not affected
  Spec store bypass:         Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:                Mitigation; usercopy/swapgs barriers and __user pointer s
                             anitization
  Spectre v2:                Mitigation; Enhanced / Automatic IBRS; IBPB conditional; 
                             PBRSB-eIBRS Not affected; BHI BHI_DIS_S
  Srbds:                     Not affected
  Tsa:                       Not affected
  Tsx async abort:           Not affected
  Vmscape:                   Mitigation; IBPB before exit to userspace
```

```bash
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
zram0       253:0    0     4G  0 disk [SWAP]
nvme0n1     259:0    0 931.5G  0 disk 
├─nvme0n1p1 259:1    0     1G  0 part /boot
└─nvme0n1p2 259:2    0 930.5G  0 part /
```

```bash
$ lsusb
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 003 Device 002: ID 06cb:00f9 Synaptics, Inc. 
Bus 003 Device 003: ID 5986:2178 Bison Electronics Inc. Integrated RGB Camera
Bus 003 Device 004: ID 8087:0037 Intel Corp. 
Bus 003 Device 017: ID 17ef:1176 Lenovo USB2.1 Hub
Bus 003 Device 018: ID 17ef:1178 Lenovo USB2.1 Hub
Bus 003 Device 019: ID 0bda:8156 Realtek Semiconductor Corp. USB 10/100/1G/2.5G LAN
Bus 003 Device 020: ID 17ef:4c41 Lenovo USB_HID_DEVICE
Bus 003 Device 021: ID 0bda:1299 Realtek Semiconductor Corp. HID Device
Bus 003 Device 022: ID 17ef:f00f Lenovo MonitorController
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
```

```bash
$ lspci
00:00.0 Host bridge: Intel Corporation Core Ultra 200U Series Processors with 2 P-Cores 8 E-Cores Host Bridge (rev 05)
00:02.0 VGA compatible controller: Intel Corporation Arrow Lake-U [Intel Graphics]
00:04.0 Signal processing controller: Intel Corporation Core Ultra 200H/200V Series Processors Dynamic Tuning Technology (DTT) (rev 05)
00:06.0 PCI bridge: Intel Corporation Core Ultra 200H/200V Series Processors PCIe Root Port #9 (PXPC)
00:06.1 PCI bridge: Intel Corporation Core Ultra 200 Series Processors PCIe Root Port #10 (rev 10)
00:07.0 PCI bridge: Intel Corporation Core Ultra 200 Series Processors USB Type-C Subsystem PCIe Root Port #16 (rev 02)
00:07.2 PCI bridge: Intel Corporation Core Ultra 200 Series Processors USB Type-C Subsystem PCIe Root Port #18 (rev 02)
00:08.0 System peripheral: Intel Corporation Core Ultra 200H/200V Series Processors Gaussian & Neural-Network Accelerator (GNA)
00:0a.0 Signal processing controller: Intel Corporation Core Ultra 200H/200V Series Processors Platform Monitoring Technology (PMT) (rev 01)
00:0b.0 Processing accelerators: Intel Corporation Core Ultra 200H/200V Series Processors NPU (rev 05)
00:0d.0 USB controller: Intel Corporation Core Ultra 200 Series Processors USB xHCI (rev 02)
00:0d.2 USB controller: Intel Corporation Core Ultra 200 Series Processors Thunderbolt DMA0 (rev 02)
00:0d.3 USB controller: Intel Corporation Core Ultra 200 Series Processors Thunderbolt DMA1 (rev 02)
00:14.0 USB controller: Intel Corporation Core Ultra 200H/200V Series Processors Standalone xHCI Controller
00:14.2 RAM memory: Intel Corporation Core Ultra 200H/200V Series Processors Shared SRAM
00:14.3 Network controller: Intel Corporation Arrow Lake CNVi WiFi
00:15.0 Serial bus controller: Intel Corporation Core Ultra 200H/200V Series Processors I2C #0
00:16.0 Communication controller: Intel Corporation Core Ultra 200H/200V Series Processors CSME HECI #1
00:1c.0 PCI bridge: Intel Corporation Core Ultra 200H/200V Series Processors PCIe Root Port #1
00:1f.0 ISA bridge: Intel Corporation Device 7703
00:1f.3 Audio device: Intel Corporation Core Ultra 200H/200V Series Processors HD Audio
00:1f.4 SMBus: Intel Corporation Core Ultra 200H/200V Series Processors SMBus
00:1f.5 Serial bus controller: Intel Corporation Core Ultra 200H/200V Series Processors SPI (flash) Controller
05:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller S4LV008[Pascal]
```

### fastfetch

```bash
~ $ fastfetch
                  -`                     <user>@<host>
                 .o+`                    -----------
                `ooo/                    OS: Arch Linux x86_64
               `+oooo:                   Host: 21R2S15B00 (ThinkPad T14s Gen 6)
              `+oooooo:                  Kernel: Linux 7.1.3-arch2-2
              -+oooooo+:                 Uptime: 21 hours, 55 mins
            `/:-:++oooo+:                Packages: 578 (pacman)
           `/++++/+++++++:               Shell: bash 5.3.15
          `/++++++++++++++:              Display (P24QD-40): 2560x1440 in 24", 120 Hz [External]
         `/+++ooooooooooooo/`            Display (CSO1432): 1920x1200 in 14", 60 Hz [Built-in]
        ./ooosssso++osssssso+`           DE: sway:wlroots
       .oossssso-````/ossssss+`          WM: Sway 1.12 (Wayland)
      -osssssso.      :ssssssso.         Cursor: Adwaita
     :osssssss/        osssso+++.        Terminal: foot 1.27.0
    /ossssssss/        +ssssooo/-        Terminal Font: monospace (8pt)
  `/ossssso+/:-        -:/+osssso+-      CPU: Intel(R) Core(TM) Ultra 7 255U (4+8+2) @ 5.20 GHz
 `+sso+:-`                 `.-/+oso:     GPU: Intel Graphics @ 2.10 GHz [Integrated]
`++:.                           `-/+/    Memory: 4.49 GiB / 30.83 GiB (15%)
.`                                 `/    Swap: 0 B / 4.00 GiB (0%)
                                         Disk (/): 8.16 GiB / 914.83 GiB (1%) - ext4
                                         Local IP (wlan0): 0.0.0.0.
                                         Battery (<redacted>): 80% [AC Connected]
                                         Locale: en_US.UTF-8
```