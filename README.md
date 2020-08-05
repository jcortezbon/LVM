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

    `$ lvs`
    
- Si no lo tienes instalalo:
    
    `$ sudo yum makecache`
        
    `$ sudo yum install lvm2`

2. Tener Privilegios sudo o ser root
3. Listar los discos existentes:

    `$ lsblk`

    `$ fdisk -l`

4. Initializing Disk for LVM: Formatear los discos y crear la(s) particion(es) (1,2...n) de tipo LVM o 8e y seguir el asistente. Ejm. para el disco "sdb": 

    `$ fdisk /dev/sdb`

- Now type in n and press <Enter> to create a new partition. Now keep pressing <Enter> to accept the defaults.
- The partition should be created.
- Now type in t and press <Enter>. Then type in 8e as the Hex code and press <Enter>. The partition type should be set to Linux LVM.
- Now type in w and press <Enter> to save the changes.
- The partition /dev/sdb1 is now ready to be used with LVM.

5. Adding the Disk to LVM PV: Ejecutamos el siguiente comando para agregar el disco /dev/sdb1 al LVM como PV (se pueden agregar mas discos en la misma linea): 

    `$ pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1 /dev/sdf1`

- You can Scanning for Block Devices: 

    `$ lvmdiskscan`

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

- hay 3 comandos que se usan para listar y mostrar propiedades de los physical volumes en LVM: pvs, pvdisplay, and pvscan

6. Creating Volume Group (VG): Creamos los 2 Volume Group "vg-docker" "vg-extradata" con 2 PV para cada grupo: 

    `$ vgcreate vg-docker /dev/sdb1 /dev/sdc1`

    `$ vgcreate vg-extradata /dev/sdd1 /dev/sde1`

- Mostrar info de Volume Group: 

    `$ vgdisplay`

    `$ vgs`

    `$ vgscan`

7. Creating Logical Volumes: Ahora podemos crear tantos LV como desee utilizando un VG en este caso creamos a cada VG un LV de 2GB para cada uno:

- Crear Logical Volume "log_vol_1" con 2GB para vg-docker:

    `$ lvcreate -L 2048M vg-docker -n log_vol_1`

- Crear Logical Volume "log_vol_2" con 2GB en el vg-extradata: 

    `$ lvcreate -L 2048M vg-extradata -n log_vol_2`

- Mostrar LV: 

    `$ lvdisplay`

    `$ lvs`

    `$ lvscan`

8. Formatting and Mounting Logical Volumes: Los LV pueden accederse tal como lo hace con particiones de disco duro ordinario. Sintaxis: /dev/VG_NAME/LV_NAME

- Corre el siguiente comando para formatear /dev/vg-docker/log_vol_1 LV to xfs filesystem:

    `$ mkfs.xfs /dev/vg-docker/log_vol_1`

- Ahora ejecutamos el siguiente comando para crear un punto de montaje donde se desee montar el LV /dev/vg-docker/log_vol_1

    `$ mkdir -pv /mnt/docker`

- Ahora podemos montar /dev/vg-docker/log_vol_1 a cualquier directorio vacio como /mnt/docker

    `$ mount /dev/vg-docker/log_vol_1 /mnt/docker`

9. Con el sgt. comando podemos ver que el LV esta montado en el punto de montaje deseado:

    `$ df -h`

        [root@vm1 vagrant]# df -h
        Filesystem                        Size  Used Avail Use% Mounted on
        devtmpfs                          489M     0  489M   0% /dev
        tmpfs                             496M     0  496M   0% /dev/shm
        tmpfs                             496M  6.8M  489M   2% /run
        tmpfs                             496M     0  496M   0% /sys/fs/cgroup
        /dev/sda1                          40G  3.4G   37G   9% /
        vdata                             901G  146G  755G  17% /vdata
        tmpfs                             100M     0  100M   0% /run/user/1000
        /dev/mapper/vg--docker-log_vol_1  8.0G   33M  8.0G   1% /mnt/docker

10. Adicional:

- Extending Volume Groups: Si deseas puedes agregar mas PV a un VG existente con el siguiente comando:

    `vgextend name_VG /dev/sdxy`

    xy = PV disponible

