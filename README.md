# LVM
- On LVM, volume groups are like storage pools and they aggregate together the capacity of multiple storage devices.
- Logical volumes reside on volume groups and can span multiple physical disks.


## Outline of Steps

- Using fdisk create partitions with partition ID of LVM
- Once those partitions or disks are avalible, they need to be set up as physical volumes (PVs).
that process initializes a disk or prtition for use by LVM.
- Then, you create volume groups (VGs) grom one or more physical volumes.
- VGs organize the physical storage in a collection of disk chunks known as physical extents (PEs).
Eith the right commands, you can then organize those PEs into logical volumes (LVs).

## Definitions in LVM

- Logical volumes are made of logical extents (LEs), which map to the underlining PEs.
- You can then format and mount the LVs.
Physical volume (PV) A PV is a partition or disk drive initialized to be used LVM
Physical extent (PE) A PE is a small uniform segment of disk space. PVs are split into PEs.
Volume group (VG)    A VL is a storage pool, made of one or more PVs.
Logical extent (LE)  Every PE is associated with an LE, and these PEs can be combined into a logical volume.
Logical volume (LV)  An LV is a part of a VG and is made of LEs. An LV can be formatted with a filesystem and then mounted on the directory of your choice.

## Laboratorio:
- Crear un VM con 5 discos atachados
- Crear 2 VolumeGroups:
    - vg-docker
    - vg-extradata
- Crear un LogicalVolume de 2GB en cada VG
- Montar un FileSystem a los VG de tipo XFS

## Pasos:

1. Verificar que tienes LVM

    `lvs`
    
- Si no lo tienes instalalo:
    
    `sudo yum makecache`
        
    `sudo yum install lvm2`

2. Tener Privilegios sudo o ser root
3. Listar los discos existentes:

    `lsblk`

    `fdisk -l`

4. Formatear los discos y crear la(s) particion(es) (1,2...n) y el ID de LVM (8e) y seguir el asistente. Ejm. para disco "sdb": 

    `fdisk /dev/sdb`

5. Crear PV: Agregamos el disco o la(s) particion(es) al LVM physical volume: 

    `pvcreate /dev/sdb1 /dev/sdc1`

6. You can Scanning for Block Devices: 

    `lvmdiskscan`

        [root@vm1 vagrant]# lvmdiskscan
        /dev/sda1 [     <40.00 GiB] 
        /dev/sdb1 [      <5.00 GiB] LVM physical volume
        /dev/sdc1 [      <5.00 GiB] LVM physical volume
        /dev/sdd1 [      <5.00 GiB] LVM physical volume
        /dev/sde1 [      <5.00 GiB] LVM physical volume
        /dev/sdf1 [      <5.00 GiB] LVM physical volume
        0 disks
        1 partition
        0 LVM physical volume whole disks
        5 LVM physical volumes

7. Puedes mostrar Physical Volume; hay 3 comandos que se usan para mostrar propiedades de physical volumes LVM: pvs, pvdisplay, and pvscan

8. Crea 2 volume group name vg-docker y vg-extradata, asignandole 2 particion a cada grupo: sudo vgcreate vg-docker /dev/sdd1 /dev/sde1

sudo vgcreate vg-extradata /dev/sdd1 /dev/sde1

- Mostrar info de Volume Group: sudo vgdisplay 
- Crear Logical Volume "log_vol_1" con 2GB en el vg-docker: sudo lvcreate -L 2048M vg-docker -n log_vol_1
- Mostrar LV: sudo lvdisplay 
- Crear Logical Volume "log_vol_2" con 2GB en el vg-extradata: sudo lvcreate -L 2048M vg-extradata -n log_vol_2
