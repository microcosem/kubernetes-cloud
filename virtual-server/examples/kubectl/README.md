This directory contains several example manifests for `VirtualServer`.

To run any of the examples issue: `kubectl apply -f <manifest_file_name.yaml>`

CoreWeave provides base images for different operating systems, including images pre-loaded with NVIDIA drivers and remote desktop software. Refer to the [System Images Documentation](https://docs.coreweave.com/virtual-servers/coreweave-system-images) to learn how to list these images via CLI.

- [virtual-server-direct-attach-lb.yaml](virtual-server-direct-attach-lb.yaml) shows how to directly attach the Load Balancer IP to a Virtual Server. This will the VS a unfiltered Public IP that also is visible to the VM itself. This will give a classic VPS style experience.

- [virtual-server-windows-internal-ip-only.yaml](virtual-server-windows-internal-ip-only.yaml) creates a Windows Virtual Server with no public IP (STATIC internal IP only) - useful for servers that will only be accessed in your namespace, such as Domain Controllers.

- [virtual-server-windows-cpu-only.yaml](virtual-server-windows-cpu-only.yaml) creates a Windows Virtual Server with no GPU - CPU compute only.

- [virtual-server-shared-pvc.yaml](virtual-server-shared-pvc.yaml) attaches shared `PVC` to the Virtual Server. The `PVC` formated already and mounted as `/mnt/shared-pvc`.

- [virtual-server-ephemeral-root-disk.yaml](virtual-server-ephemeral-root-disk.yaml) boots a Virtual Server from a root-disk image in ephemeral mode. Changes to the VM root disk will be written to local node ephemeral storage, and lost on restart. Useful for epehemeral tasks such as pixel-streaming and data-processing.

- [virtual-server-windows.yaml](virtual-server-windows.yaml) creates Windows10 Virtual Server. To get the external IP and use remote desktop via `RDP` protocol, issue the following command:
  ```
  kubectl get svc vs-windows10-tcp -o jsonpath="{.status.loadBalancer.ingress[*].ip}"
  ```

- [virtual-server-block-pvc.yaml](virtual-server-block-pvc.yaml) attaches an additional block `PVC` disk to the virtual machine. The new disk is raw and needs to be formatted.

  ```

  myuser@vs-ubuntu2004-block-pvc:~$ sudo mkfs.ext4 /dev/vdb
  mke2fs 1.45.5 (07-Jan-2020)
  Discarding device blocks: done                            
  Creating filesystem with 5242880 4k blocks and 1310720 inodes
  Filesystem UUID: 0a05b295-9518-41f3-8b64-18d5902d419e
  Superblock backups stored on blocks: 
    32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
    4096000

  Allocating group tables: done                            
  Writing inode tables: done                            
  Creating journal (32768 blocks): done
  Writing superblocks and filesystem accounting information: done 

  ```

  Now, the disk is ready:
  ```
  myuser@vs-ubuntu2004-block-pvc:~$ sudo mkdir /mnt/vdb && sudo mount /dev/vdb /mnt/vdb
  myuser@vs-ubuntu2004-block-pvc:~$ df -h
  Filesystem      Size  Used Avail Use% Mounted on
  udev            7.9G     0  7.9G   0% /dev
  tmpfs           1.6G  1.1M  1.6G   1% /run
  /dev/vda1        39G  2.6G   37G   7% /
  tmpfs           7.9G     0  7.9G   0% /dev/shm
  tmpfs           5.0M     0  5.0M   0% /run/lock
  tmpfs           7.9G     0  7.9G   0% /sys/fs/cgroup
  /dev/vda15      105M  7.8M   97M   8% /boot/efi
  /dev/loop0       71M   71M     0 100% /snap/lxd/19647
  /dev/loop1       56M   56M     0 100% /snap/core18/1988
  /dev/loop2       33M   33M     0 100% /snap/snapd/11107
  /dev/loop3       33M   33M     0 100% /snap/snapd/12704
  /dev/loop4       56M   56M     0 100% /snap/core18/2128
  /dev/loop5       71M   71M     0 100% /snap/lxd/21029
  tmpfs           1.6G     0  1.6G   0% /run/user/1001
  /dev/vdb         20G   45M   19G   1% /mnt/vdb
  ```

Additional examples and documentation:

- [Kubernetes documentation](https://docs.coreweave.com/coreweave-kubernetes/getting-started)
- [VirtualServer documentation](https://docs.coreweave.com/virtual-servers/getting-started)
- [Advanced Label selectors](https://docs.coreweave.com/coreweave-kubernetes/label-selectors)
- [CPU and GPU Availability](https://docs.coreweave.com/coreweave-kubernetes/node-types)
- [Storage](https://docs.coreweave.com/coreweave-kubernetes/storage)

