static void *virt_addr;
static dma_addr_t iova_addr;
phys_addr_t phys_addr;
static int nvme_driver_probe(struct pci_dev *pdev, const struct pci_device_id *ent)
{
        int bar, err;
        u16 vendor, device;
        unsigned long mmio_start,mmio_len;
        u32 temp;
        int i;

printk(KERN_INFO " ops = %p \n", pdev->dev.dma_ops);

virt_addr = dma_alloc_coherent(&pdev->dev, PAGE_SIZE, &iova_addr, GFP_KERNEL | GFP_DMA | GFP_ATOMIC);
printk("virt_addr %p\n",virt_addr);
phys_addr = virt_to_phys(virt_addr);
printk("phys =%pap iova =%pad\n",&phys_addr,&iova_addr);
printk("virt_addr_valid %d\n",virt_addr_valid(virt_addr));
return 0;
}


echo 'module iommu +p' > /sys/kernel/debug/dynamic_debug/control

with iommu=pt
[365565.825921]  ops = 0
[365565.825928] virt_addr ff20a4f7d333d000
[365565.825929] phys =0x000000011333d000 iova =0x000000011333d000
[365565.825930] is virt_addr_valid 1

ff11000000000000 |  -59.75 PB | ff90ffffffffffff |   32 PB | direct mapping of all physical memory (page_offset_base)


 echo dma_alloc_attrs dma_alloc_from_contiguous alloc_pages dma_alloc_pages dma_alloc_contiguous dma_direct_alloc dma_direct_alloc_pages cma_alloc_aligned __dma_direct_alloc_pages.constprop.0 cma_alloc > set_ftrace_filter
 echo 5 > max_graph_depth
 echo function_graph > current_tracer
 
 echo 'module cma +p' > /sys/kernel/debug/dynamic_debug/control
 
   0)               |  dma_alloc_attrs() {
  0)               |    dma_direct_alloc() {
  0)               |      __dma_direct_alloc_pages.constprop.0() {
  0)   0.389 us    |        dma_alloc_contiguous();
  0)   1.578 us    |      }
  0)   2.274 us    |    }
  0)   4.782 us    |  }


no iommu=pt
[  236.898860]  ops = 000000003ae57b2e
[  236.898865] iommu: map: iova 0xfcbbf000 pa 0x00000008b2f33000 size 0x1000
[  236.898866] iommu: mapping: iova 0xfcbbf000 pa 0x00000008b2f33000 pgsize 0x1000 count 1
[  236.898873] virt_addr 0000000093a6a62f
[  236.898873] phys =0x002743cc045d9000 iova =0x00000000fcbbf000
[  236.898874] virt_addr_valid 0
[  236.898875] Device vid: 0x14E4 pid: 0x1760



on ubuntu machine

CONFIG_MAILBOX=y
CONFIG_PCC=y
CONFIG_ALTERA_MBOX=m
CONFIG_IOMMU_IOVA=y
CONFIG_IOASID=y
CONFIG_IOMMU_API=y
CONFIG_IOMMU_SUPPORT=y

#
# Generic IOMMU Pagetable Support
#
# end of Generic IOMMU Pagetable Support

CONFIG_IOMMU_DEBUGFS=y
# CONFIG_IOMMU_DEFAULT_DMA_STRICT is not set
CONFIG_IOMMU_DEFAULT_DMA_LAZY=y
# CONFIG_IOMMU_DEFAULT_PASSTHROUGH is not set
CONFIG_IOMMU_DMA=y
CONFIG_IOMMU_SVA_LIB=y
# CONFIG_AMD_IOMMU is not set
CONFIG_DMAR_TABLE=y
CONFIG_DMAR_PERF=y
CONFIG_INTEL_IOMMU=y
CONFIG_INTEL_IOMMU_DEBUGFS=y
CONFIG_INTEL_IOMMU_SVM=y
CONFIG_INTEL_IOMMU_DEFAULT_ON=y
CONFIG_INTEL_IOMMU_FLOPPY_WA=y
CONFIG_INTEL_IOMMU_SCALABLE_MODE_DEFAULT_ON=y
CONFIG_IRQ_REMAP=y
CONFIG_HYPERV_IOMMU=y
CONFIG_VIRTIO_IOMMU=y


[  232.850641]  ops = 000000009b5b1b3e
[  232.850645] iommu: map: iova 0xfffff000 pa 0x0000000182cac000 size 0x1000
[  232.850647] iommu: mapping: iova 0xfffff000 pa 0x0000000182cac000 pgsize 0x1000 count 1
[  232.850651] virt_addr 0000000040edad9d
[  232.850652] phys =0x0000000182cac000 iova =0x00000000fffff000
[  232.850653] virt_addr_valid 1

Device 0000:22:00.0 with pasid 0 @0x10ceda000
IOVA_PFN                PML5E                   PML4E                   PDPE                    PDE                     PTE
0x00000000fffff |       0x0000000000000000      0x8000000107eeb027      0x800000019af5f027      0x80000001842ee027      0x8000000182cac867
PFN for iova address 0xfffff000


2MB
[262040.668700] iommu: map: iova 0x200000 pa 0x0000000694200000 size 0x200000
[262040.668702] iommu: mapping: iova 0x200000 pa 0x0000000694200000 pgsize 0x200000 count 1

Device 0000:22:00.0 with pasid 0 @0x148b9e000
IOVA_PFN                PML5E                   PML4E                   PDPE                    PDE                     PTE
0x0000000000200 |       0x0000000000000000      0x00000001222f2003      0x0000000146580003      0x0000000694200883      0x0000000000000000


no iommu=pt
[81234.281509]  ops = a87431e0
[81234.281514] iommu: map: iova 0xda9ec000 pa 0x000000014a6ed000 size 0x1000
[81234.281515] iommu: mapping: iova 0xda9ec000 pa 0x000000014a6ed000 pgsize 0x1000 count 1
[81234.281523] virt_addr ff2d5aa9403bb000
[81234.281523] phys =0x0004213cc03bb000 iova =0x00000000da9ec000
[81234.281524] is virt_addr_valid 0
[81234.281525] Device vid: 0x14E4 pid: 0x1760





trace_event=iommu
                                                     map: IOMMU: iova=0x0000000000000000 - 0x0000000000002000 paddr=0x00000001110f8000 size=8192
        qemu-kvm-4575    [008] .......   438.308478: unmap: IOMMU: iova=0x0000000000000000 - 0x0000000000001000 size=4096 unmapped_size=4096
        qemu-kvm-4575    [008] .......   438.308480: unmap: IOMMU: iova=0x0000000000001000 - 0x0000000000002000 size=4096 unmapped_size=4096
        qemu-kvm-4575    [008] .......   438.308514: map: IOMMU: iova=0x0000000000000000 - 0x00000000000c0000 paddr=0x00000001a6800000 size=786432
        qemu-kvm-4575    [008] .......   438.308516: map: IOMMU: iova=0x00000000000c0000 - 0x00000000000c1000 paddr=0x00000001a68c0000 size=4096
        qemu-kvm-4575    [008] .......   438.308518: map: IOMMU: iova=0x00000000000c1000 - 0x00000000000c4000 paddr=0x00000001a68c1000 size=12288
        qemu-kvm-4575    [008] .......   438.308520: map: IOMMU: iova=0x00000000000c4000 - 0x00000000000e8000 paddr=0x00000001a68c4000 size=147456
        qemu-kvm-4575    [008] .......   438.308521: map: IOMMU: iova=0x00000000000e8000 - 0x00000000000f0000 paddr=0x00000001a68e8000 size=32768
        qemu-kvm-4575    [008] .......   438.308522: map: IOMMU: iova=0x00000000000f0000 - 0x0000000000100000 paddr=0x00000001a68f0000 size=65536
        qemu-kvm-4575    [008] .......   438.308529: map: IOMMU: iova=0x0000000000100000 - 0x0000000000200000 paddr=0x00000001a6900000 size=1048576
        qemu-kvm-4575    [008] .......   438.308535: map: IOMMU: iova=0x0000000000200000 - 0x0000000000400000 paddr=0x0000000197a00000 size=2097152
        qemu-kvm-4575    [008] .......   438.308541: map: IOMMU: iova=0x0000000000400000 - 0x0000000000600000 paddr=0x0000000197800000 size=2097152
        qemu-kvm-4575    [008] .......   438.314784: map: IOMMU: iova=0x000000007fc00000 - 0x000000007fe00000 paddr=0x00000008b1000000 size=2097152
        qemu-kvm-4575    [008] .......   438.314789: map: IOMMU: iova=0x000000007fe00000 - 0x0000000080000000 paddr=0x0000000896a00000 size=2097152
        qemu-kvm-4575    [008] .......   438.314809: map: IOMMU: iova=0x00000000fffc0000 - 0x00000000fffc1000 paddr=0x000000014c17f000 size=4096


(VF assign to VM with ATS disabled
cat /sys/kernel/debug/iommu/intel/0000:b6:00.0/domain_translation_struct)
Device 0000:b6:00.0 with pasid 0 @0x14b351000
IOVA_PFN                PML5E                   PML4E                   PDPE                    PDE                     PTE
0x00000000001fb |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012d590003      0x00000001539fb803
0x00000000001fc |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012d590003      0x00000001539fc803
0x00000000001fd |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012d590003      0x00000001539fd803
0x00000000001fe |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012d590003      0x00000001539fe803
0x00000000001ff |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012d590003      0x00000001539ff803
0x0000000000200 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x0000000134400883
0x0000000000400 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x0000000124c00883
0x0000000000600 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000011b400883
0x0000000000800 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x0000000126200883
0x0000000000a00 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000011f200883
0x0000000000c00 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x0000000146a00883
0x0000000000e00 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000012f200883
0x0000000001000 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x0000000148800883
0x0000000001200 |       0x0000000000000000      0x0000000117072003      0x0000000109b9c003      0x000000010fc00883

b5-ATS b6-no ATS
IOMMU dmar2: Root Table Address: 0x88749c000
B.D.F   Root_entry                              Context_entry                           PASID   PASID_table_entry
b4:00.4 0x0000000000000000:0x00000008874bd001   0x0000000000000000:0x00000008874bc401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b4:01.0 0x0000000000000000:0x00000008874bd001   0x0000000000000000:0x00000008874bf401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b5:00.0 0x0000000000000000:0x00000008874c1001   0x0000000000000000:0x00000008874c0405 (5 - device tlb enable)  0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b6:00.0 0x0000000000000000:0x00000008aa7f9001   0x0000000000000000:0x00000008c592b401 (1 - device tlb disable)  0       0x0000000000000000:0x0000000000800003:0x000000014b351089

b5-ATS b6-ATS
IOMMU dmar2: Root Table Address: 0x8866e6000
B.D.F   Root_entry                              Context_entry                           PASID   PASID_table_entry
b4:00.4 0x0000000000000000:0x00000008866fe001   0x0000000000000000:0x00000008866fd401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b4:01.0 0x0000000000000000:0x00000008866fe001   0x0000000000000000:0x00000008866ff401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b5:00.0 0x0000000000000000:0x0000000886701001   0x0000000000000000:0x0000000886700405   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b6:00.0 0x0000000000000000:0x000000089cbe3001   0x0000000000000000:0x000000089cf50405   0       0x0000000000000000:0x0000000000800001:0x0000000000000109


Intel vt-d rev3.0 [1] introduces a new translation mode called
'scalable mode', which enables PASID-granular translations for
first level, second level, nested and pass-through modes. The
vt-d scalable mode is the key ingredient to enable Scalable I/O
Virtualization (Scalable IOV) [2] [3], which allows sharing a
device in minimal possible granularity (ADI - Assignable Device
Interface). It also includes all the capabilities required to
enable Shared Virtual Addressing (SVA). As a result, previous
Extended Context (ECS) mode is deprecated (no production ever
implements ECS).

Each scalable mode pasid table entry is 64 bytes in length, with
fields point to the first level page table and the second level
page table. The PGTT (Pasid Granular Translation Type) field is
used by hardware to determine the translation type.

(PASID table entry)
          A Scalable Mode            .-------------.
           PASID Entry             .-|             |
       .------------------.      .-| | 1st Level   |
      7|                  |      | | | Page Table  |
       .------------------.      | | |             |
      6|                  |      | | |             |
       '------------------'      | | '-------------'
      5|                  |      | '-------------'
       '------------------'      '-------------'
      4|                  |    ^
       '------------------'   /
      3|                  |  /       .-------------.
       .----.-------.-----. /      .-|             |
      2|    | FLPTR |     |/     .-| | 2nd Level   |
       .----'-------'-----.      | | | Page Table  |
      1|                  |      | | |             |
       .-.-------..------..      | | |             |
      0| | SLPTR || PGTT ||----> | | '-------------'
       '-'-------''------''      | '-------------'
       6             |    0      '-------------'
       3             v
             .------------------------------------.
             | PASID Granular Translation Type    |
             |                                    |
             | 001b: 1st level translation only   |
             | 101b: 2nd level translation only   |
             | 011b: Nested translation           |
             | 100b: Pass through                 |
             '------------------------------------'

b5:00.0 PF- ats, b6:00.0 VF - no ats
before assigning vf (b6:00.0) to VM
IOMMU dmar2: Root Table Address: 0x886dfe000
B.D.F   Root_entry                              Context_entry                           PASID   PASID_table_entry
b4:00.4 0x0000000000000000:0x0000000886e21001   0x0000000000000000:0x0000000886e20401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b4:01.0 0x0000000000000000:0x0000000886e21001   0x0000000000000000:0x0000000886e23401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b5:00.0 0x0000000000000000:0x0000000886e25001   0x0000000000000000:0x0000000886e24405   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b6:00.0 0x0000000000000000:0x000000089f644001   0x0000000000000000:0x0000000885113401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109 ( pass through)

b5:00.0 PF- ats, b6:00.0 VF - no ats
After assigning vf (b6:00.0) to VM
IOMMU dmar2: Root Table Address: 0x886dfe000
B.D.F   Root_entry                              Context_entry                           PASID   PASID_table_entry
b4:00.4 0x0000000000000000:0x0000000886e21001   0x0000000000000000:0x0000000886e20401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b4:01.0 0x0000000000000000:0x0000000886e21001   0x0000000000000000:0x0000000886e23401   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b5:00.0 0x0000000000000000:0x0000000886e25001   0x0000000000000000:0x0000000886e24405   0       0x0000000000000000:0x0000000000800001:0x0000000000000109
b6:00.0 0x0000000000000000:0x000000089f644001   0x0000000000000000:0x0000000885113401   0       0x0000000000000000:0x0000000000800003:0x0000000129c21089 ( second stage translation only)



Qemu monitor:

 qemu-kvm-7254    [077] ....... 17317.899230: map: IOMMU: iova=0x0000000004400000 - 0x0000000004800000 paddr=0x00000008db400000 size=4194304

(qemu) gpa2hpa 0x00000000044e7000
Host physical address for 0x44e7000 (pc.ram) is 0x8db4e7000
(qemu)


with VM+ vf PT
sudo virsh start
[ 1189.113527] iommu: map: iova 0x6e200000 pa 0x00000004f4800000 size 0x400000
[ 1189.113527] iommu: mapping: iova 0x6e200000 pa 0x00000004f4800000 pgsize 0x200000 count 2
[ 1189.113585] iommu: map: iova 0x6e600000 pa 0x00000004cd800000 size 0x400000
[ 1189.113585] iommu: mapping: iova 0x6e600000 pa 0x00000004cd800000 pgsize 0x200000 count 2
[ 1189.113643] iommu: map: iova 0x6ea00000 pa 0x00000004e5400000 size 0x400000
[ 1189.113643] iommu: mapping: iova 0x6ea00000 pa 0x00000004e5400000 pgsize 0x200000 count 2
[ 1189.113701] iommu: map: iova 0x6ee00000 pa 0x0000000531400000 size 0x400000
[ 1189.113702] iommu: mapping: iova 0x6ee00000 pa 0x0000000531400000 pgsize 0x200000 count 2
[ 1189.113760] iommu: map: iova 0x6f200000 pa 0x0000000505000000 size 0x400000
[ 1189.113761] iommu: mapping: iova 0x6f200000 pa 0x0000000505000000 pgsize 0x200000 count 2
[ 1189.113818] iommu: map: iova 0x6f600000 pa 0x0000000512800000 size 0x400000
[ 1189.113819] iommu: mapping: iova 0x6f600000 pa 0x0000000512800000 pgsize 0x200000 count 2
[ 1189.113877] iommu: map: iova 0x6fa00000 pa 0x0000000541400000 size 0x400000
[ 1189.113877] iommu: mapping: iova 0x6fa00000 pa 0x0000000541400000 pgsize 0x200000 count 2
[ 1189.113935] iommu: map: iova 0x6fe00000 pa 0x0000000522000000 size 0x400000

sudo virsh destroy
64.463349] iommu: unmapped: iova 0x1a600000 size 0x400000
[ 2764.463359] iommu: unmap this: iova 0x1aa00000 size 0x400000
[ 2764.463360] iommu: unmapped: iova 0x1aa00000 size 0x400000
[ 2764.463370] iommu: unmap this: iova 0x1ae00000 size 0x400000
[ 2764.463371] iommu: unmapped: iova 0x1ae00000 size 0x400000
[ 2764.463381] iommu: unmap this: iova 0x1b200000 size 0x400000
[ 2764.463381] iommu: unmapped: iova 0x1b200000 size 0x400000
[ 2764.463391] iommu: unmap this: iova 0x1b600000 size 0x400000
[ 2764.463392] iommu: unmapped: iova 0x1b600000 size 0x400000
[ 2764.463402] iommu: unmap this: iova 0x1ba00000 size 0x400000
[ 2764.463403] iommu: unmapped: iova 0x1ba00000 size 0x400000
[ 2764.463413] iommu: unmap this: iova 0x1be00000 size 0x400000
[ 2764.463413] iommu: unmapped: iova 0x1be00000 size 0x400000
[ 2764.463423] iommu: unmap this: iova 0x1c200000 size 0x400000
[ 2764.463424] iommu: unmapped: iova 0x1c200000 size 0x400000
[ 2764.463434] iommu: unmap this: iova 0x1c600000 size 0x400000
[ 2764.463434] iommu: unmapped: iova 0x1c600000 size 0x400000
[ 2764.463445] iommu: unmap this: iova 0x1ca00000 size 0x400000
[ 2764.463445] iommu: unmapped: iova 0x1ca00000 size 0x400000
[ 2764.463455] iommu: unmap this: iova 0x1ce00000 size 0x400000
[ 2764.463456] iommu: unmapped: iova 0x1ce00000 size 0x400000
[ 2764.463466] iommu: unmap this: iova 0x1d200000 size 0x400000
[ 2764.463467] iommu: unmapped: iova 0x1d200000 size 0x400000
[ 2764.463477] iommu: unmap this: iova 0x1d600000 size 0x400000
[ 2764.463477] iommu: unmapped: iova 0x1d600000 size 0x400000
[ 2764.463487] iommu: unmap this: iova 0x1da00000 size 0x400000
[ 2764.463488] iommu: unmapped: iova 0x1da00000 size 0x400000
[ 2764.463498] iommu: unmap this: iova 0x1de00000 size 0x400000



[root@localhost brcm]# cat /sys/kernel/tracing/trace
# tracer: nop
#
# entries-in-buffer/entries-written: 7/7   #P:40
#
#                                _-------=> irqs-off/BH-disabled
#                               / _------=> need-resched
#                              | / _-----=> need-resched-lazy
#                              || / _----=> hardirq/softirq
#                              ||| / _---=> preempt-depth
#                              |||| / _--=> preempt-lazy-depth
#                              ||||| / _-=> migrate-disable
#                              |||||| /     delay
#           TASK-PID     CPU#  |||||||  TIMESTAMP  FUNCTION
#              | |         |   |||||||      |         |
       swapper/0-1       [002] ....1..     0.831928: my: (pci_enable_ats+0x0/0xa0) a=8 v=32902 d=2853 ( 0x0b25)
       swapper/0-1       [002] ....1..     0.831939: my: (pci_enable_ats+0x0/0xa0) a=16 v=32902 d=3326 ( 0x0cfe)
       swapper/0-1       [002] ....1..     0.831942: my: (pci_enable_ats+0x0/0xa0) a=0 v=32902 d=18754 ( 0x4942)
       swapper/0-1       [002] ....1..     0.832563: my: (pci_enable_ats+0x0/0xa0) a=0 v=5348 d=5984
       swapper/0-1       [002] ....1..     0.832959: my: (pci_enable_ats+0x0/0xa0) a=8 v=32902 d=2853
       swapper/0-1       [002] ....1..     0.832963: my: (pci_enable_ats+0x0/0xa0) a=16 v=32902 d=3326
       swapper/0-1       [002] ....1..     0.832967: my: (pci_enable_ats+0x0/0xa0) a=0 v=32902 d=18754


[root@localhost brcm]# cat /proc/cmdline
BOOT_IMAGE=(hd0,gpt2)/vmlinuz-5.14.0-505.el9.x86_64 root=/dev/mapper/cs_dhcp--10--193--93--110-root ro crashkernel=1G-4G:192M,4G-64G:256M,64G-:512M resume=/dev/mapper/cs_dhcp--10--193--93--110-swap rd.lvm.lv=cs_dhcp-10-193-93-110/root rd.lvm.lv=cs_dhcp-10-193-93-110/swap rhgb quiet intel_iommu=on iommu=pt tp_printk trace_buf_size=1M kprobe_event=p:kprobes/my,pci_enable_ats,a=+56($arg1):u32,v=+60($arg1):u16,d=+62($arg1):u16


 


"ddebug_query=file drivers/iommu/intel/iommu.c +p"

trace_options=trace_buf_size=1M kprobe_event=”p:myprobe,pci_enable_ats,dfn=+56($arg1):u32,ven=+60($arg1):u16,dev=+60($arg1):u16"

tp_printk trace_buf_size=1M kprobe_event=p:kprobes/myevent,intel_pasid_setup_second_level

tp_printk trace_buf_size=1M kprobe_event=p:kprobes/myevent,pci_enable_ats,dfd=%ax,
tp_printk trace_buf_size=1M kprobe_event=p:kprobes/myevent,pci_enable_ats,dfn=+56(\$arg1):u32,ven=+60(\$arg1):u16,dev=+62(\$arg1):u16
tp_printk trace_buf_size=1M kprobe_event=p:kprobes/myevent,pci_enable_ats,dfn=+56(\$arg1):u32,ven=+60(\$arg1):u16,dev=+62(\$arg1):u16

r:kprobes/myevent,dmar_find_matched_atsr_unit,re=$ret
tp_printk trace_buf_size=1M kprobe_event=p:kprobes/myevent,iommu_enable_dev_iotlb,dfn=+36(\$arg1):u32,sid=+60(\$arg1):u16

(qemu) gpa2hpa 0x00000000_1e65_8000
Host physical address for 0x1e658000 (pc.ram) is 0x1_ba45_8000
(qemu) quit
iova=0x00000000_1e20_0000 - 0x00000000_1ea0_0000 paddr=0x00000001_ba00_0000 size=8388608


  0x00000000_1e65_8000
- 0x00000000_1e20_0000
-----------------------
               45_8000
 
 
 
  0x00000001_ba00_0000
+              45_8000
-----------------------
           1_ba45_8000
 
 
 
(qemu) gpa2hpa 0x00000000_0485_b000
Host physical address for 0x485b000 (pc.ram) is 0x2_2a85_b000
(qemu)

iova=0x00000000_0480_0000 - 0x0000000004c00000 paddr=0x00000002_2a80_0000 size=4194304

                                     iova                   phy
ATS translation replay             0x0485_b000  ->  0x0000_0002_2a8f_fc03 ( here size id 2M)

                                                    0x0000_0002_2a80_0000 (lower 2M as 0 from PA)
                                                                   5_b000 (lower bits of 2m from IOVA)
                                                   ----------------------
                                                              2_2a85_b000 
