# CLI

Virtual Servers are a Kubernetes Custom Resource available on CoreWeave Cloud, and as such, `kubectl` can be used to create and modify the resource. The Virtual Server manifest used in the following example is available in the [examples](https://github.com/coreweave/kubernetes-cloud/tree/master/virtual-server/examples/kubectl) section of the CoreWeave Cloud repository.

{% embed url="https://github.com/coreweave/kubernetes-cloud/tree/master/virtual-server/examples/kubectl" %}

## Understanding the Virtual Server Manifest

The Virtual Server manifest is broken into two parts, `metadata` and `spec`. The `metadata` section, as is the case with most Kubernetes manifests, contains the name of your Virtual Server and the namespace where it will be deployed. The `spec` contains configuration fields that define how the Virtual Server will be created. These fields are as follows:

{% hint style="info" %}
The following fields are within the manifest spec
{% endhint %}

| Field                                 | Type    | Description                                                                                                                                         |
| ------------------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| region                                | String  | Defines the region where the Virtual Server is deployed                                                                                             |
| os                                    | {}      | Defines the Operating System type                                                                                                                   |
| os.type                               | String  | The Operating System type - Linux/Windows                                                                                                           |
| os.enableUEFIBoot                     | Boolean | Enable the UEFI bootloader                                                                                                                          |
| resources                             | {}      | Defines the resources and devices allocated to the Virtual Server                                                                                   |
| resources.definition                  | String  | The resource definition - defaults to 'a'                                                                                                           |
| resources.cpu                         | {}      | Defines the CPU allocation                                                                                                                          |
| resources.cpu.type                    | String  | The type of CPU to allocate                                                                                                                         |
| resources.cpu.count                   | Int     | The number of CPU cores to allocate                                                                                                                 |
| resources.gpu                         | {}      | Defines the GPU allocation                                                                                                                          |
| resources.gpu.type                    | String  | The type of GPU to allocate                                                                                                                         |
| resources.gpu.count                   | Int     | The number of GPU(s) to allocate                                                                                                                    |
| resources.memory                      | String  | The amount of memory to allocate                                                                                                                    |
| storage                               | {}      | Defines the root storage and additional storage devices                                                                                             |
| storage.root                          | {}      | Defines the root file system PVC                                                                                                                    |
| storage.root.size                     | String  | The volume size                                                                                                                                     |
| storage.root.source \[¹]              | {}      | The DataVolume source for the root file system                                                                                                      |
| storage.root.storageClassName         | String  | The storage class name for the root PVC                                                                                                             |
| storage.root.volumeMode               | String  | The volume mode for the root PVC                                                                                                                    |
| storage.root.accessMode               | String  | The access mode name for the root PVC                                                                                                               |
| storage.root.ephemeral                | Boolean | Set whether the root disk is ephemeral                                                                                                              |
| storage.additionalDisks               | \[]     | A list of PVC references to be added as disk devices                                                                                                |
| storage.additionalDisks\[ ].name      | String  | Name of the disk                                                                                                                                    |
| storage.additionalDisks\[ ].spec \[²] | {}      | The VolumeSource for the disk                                                                                                                       |
| storage.filesystems                   | \[]     | A list of PVC references to be mounted                                                                                                              |
| storage.filesystems\[ ].name          | String  | Name of the mount                                                                                                                                   |
| storage.filesystems\[ ].spec \[²]     | {}      | The VolumeSource for the file system mount                                                                                                          |
| users                                 | \[]     | A list of users to be added by cloud-init (if supported by the OS)                                                                                  |
| users\[ ].username                    | String  | Username for the user                                                                                                                               |
| users\[ ].password                    | String  | Password for the user                                                                                                                               |
| users\[ ].sshpublickey                | String  | An ssh public key for the user                                                                                                                      |
| network                               | {}      | Defines the network configuration                                                                                                                   |
| network.directAttachLoadBalancerIP    | Boolean | [Directly attach a loadbalancer IP to the Virtual Server](../../coreweave-kubernetes/exposing-applications.md#attaching-service-ip-directly-to-pod) |
| network.floatingIPs                   | \[]     | A list of service references to be added as floating IPs                                                                                            |
| network.floatingIPs\[ ].serviceName   | String  | Name of the service                                                                                                                                 |
| network.tcp                           | {}      | Defines the TCP network configuration                                                                                                               |
| network.tcp.ports                     | \[]     | List of TCP ports to expose                                                                                                                         |
| network.udp                           | {}      | Defines the UDP network configuration                                                                                                               |
| network.udp.ports                     | \[]     | List of UDP ports to expose                                                                                                                         |
| network.public                        | Boolean | Whether a public IP will be assigned                                                                                                                |
| initializeRunning                     | Boolean | The Virtual Server will be started as soon as it is created and initialized                                                                         |

{% hint style="info" %}
\[¹] - See [DataVolumeSource](https://pkg.go.dev/kubevirt.io/containerized-data-importer/pkg/apis/core/v1alpha1#DataVolumeSource) for further information

\[²] - See [VolumeSource](https://pkg.go.dev/kubevirt.io/client-go/api/v1#VolumeSource) for further information

\[³] - See [Cloud Init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html) for more examples
{% endhint %}

{% hint style="info" %}
Certain fields are mutually exclusive:

* GPU type and CPU type
* TCP ports or UDP ports and directAttachLoadbalancerIP&#x20;
{% endhint %}

## Deploying a Virtual Server

We provide a few simple examples [Virtual Server manifests](https://github.com/coreweave/kubernetes-cloud/tree/master/virtual-server/examples/kubectl).&#x20;

All examples can be easily deployed in the same way using `kubectl.` The most comprehensive Virtual server example is [virtual-server-block-pvc.yaml](../../../virtual-server/examples/kubectl/virtual-server-block-pvc.yaml).

{% hint style="warning" %}
The username and password field in the example manifests are unset. Before applying the manifest, set a secure username and password.
{% endhint %}

### Deploying a Virtual Server with an additional block disk

An additional block device can be useful for storing data in a different storage class, such as HDD storage, and can also be used to separate data that shouldn't be lost if the Virtual Server is terminated.

The example [virtual-server-block-pvc.yaml](../../../virtual-server/examples/kubectl/virtual-server-block-pvc.yaml) **** contains two manifests. The first part creates a 20Gi block type PVC with attributes `accessModes: ReadWriteOnce` and `storageClassName: block-nvme-ord1`.

You can follow along this example by simply applying that manifest.

```
kubectl apply -f virtual-server-block-pvc.yaml
```

{% hint style="info" %}
See the full list of [Storage Classes](https://docs.coreweave.com/coreweave-kubernetes/storage) for further details.
{% endhint %}

{% hint style="info" %}
See [Root Disk Lifecycle Management](https://docs.coreweave.com/virtual-servers/root-disk-lifecycle-management) how to manage the Virtual Machine disks
{% endhint %}

Once deployed, your Virtual Server will begin the initialization process. The status of your Virtual Server can be examined:

```
$ kubectl get vs vs-ubuntu2004-block-pvc
NAME                     STATUS               REASON                                           STARTED   INTERNAL IP      EXTERNAL IP
vs-ubuntu2004-block-pvc  Initializing         Waiting for VirtualMachineInstance to be ready   False                      123.123.123.123
```

Once initialization is complete, the status will then transition to VirtualServerReady.

```
$ kubectl get vs vs-ubuntu2004-block-pvc
NAME                      STATUS               REASON               STARTED   INTERNAL IP     EXTERNAL IP
vs-ubuntu2004-block-pvc   VirtualServerReady   VirtualServerReady   True      10.147.196.24   207.53.234.124
```

You can now access your Virtual Server using the`virtctl`command.&#x20;

{% hint style="info" %}
See [Remote Access and Control](https://docs.coreweave.com/virtual-servers/remote-access-and-control) for further details on how to install and use `virtctl`.
{% endhint %}

### Virtual Server Resources

Virtual Server, built upon [Kubevirt](https://kubevirt.io), deploys three resources:

* Virtual Machine

```
$ kubectl get vm vs-ubuntu2004-block-pvc
NAME                      AGE   VOLUME
vs-ubuntu2004-block-pvc   31m   

```

* Virtual Machine Instance

```
$kubectl get vmi vs-ubuntu2004-block-pvc
NAME                      AGE   PHASE     IP              NODENAME
vs-ubuntu2004-block-pvc   31m   Running   10.147.196.24   g0aa1fd
```

* `virt-launcher` pod which coexists with `vmi`&#x20;

```
$ kubectl get pods | grep virt-launcher
virt-launcher-vs-ubuntu2004-block-pvc-m8mqt      1/1     Running     0          94s
```

Depending on the current state of Virtual Server, the `vmi` will either be running or terminated.

| `virtctl` command                                  | `vs` state             | `vmi` state | `pod` state |
| -------------------------------------------------- | ---------------------- | ----------- | ----------- |
| `virtctl stop` \<name>                             | `VirtualServerStopped` | deleted     | deleted     |
| `virtctl start <name>` or `virtctl restart <name>` | `VirtualServerReady`   | running     | running     |

{% hint style="info" %}
See [Kubevirt Lifecycle](https://kubevirt.io/user-guide/virtual\_machines/lifecycle/) for further details
{% endhint %}

In our example, Virtual Server creates two additional resources:

* Block PVC

```
$ kubectl get pvc vs-ubuntu2004-block-pvc
NAME                      STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
vs-ubuntu2004-block-pvc   Bound    pvc-3ec3a05c-dc53-4f29-8f02-ff95de06f750   40Gi       RWO            block-nvme-ord1   26m
```

* Service

```
$ kubectl get svc vs-ubuntu2004-block-pvc-tcp
NAME                          TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)        AGE
vs-ubuntu2004-block-pvc-tcp   LoadBalancer   10.135.203.211   207.53.234.124   22:32849/TCP   43m
```

{% hint style="info" %}
cloudInit automatically formats and mounts the block PVC in our [example](kubectl.md#deploying-a-virtual-server-with-an-additional-block-pvc):
{% endhint %}

```bash
lsblk /dev/vdb
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
vdb    252:16   0  10G  0 disk
└─vdb1 252:17   0  10G  0 part /mnt/block-pvc
```

####

