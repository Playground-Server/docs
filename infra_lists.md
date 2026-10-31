## Device List And Specification
### Router
| Name | Brand |
| --- | --- |
| Local Mikrotik | RB750Gr3 |
| Internet | Indosat Hifi |

### Nodes
| Hostname | Hardware | Operating system | Storage | CPU / RAM | Purpose |
| --- | --- | --- | --- | --- | --- |
| util-01 | Raspberry Pi 3B+ | Ubuntu Server 22.04.2 LTS (jammy) | 16GB microSD (planned upgrade) | arm64, 4 cores, 1GB RAM | Small utility apps |
| k8s-01 | Orange Pi 5 | Ubuntu Server 22.04.2 LTS (jammy) | 250GB SSD | arm64, 8 cores, 4GB RAM | Kubernetes cluster |
| k8s-02 | Orange Pi 5 | Ubuntu Server 22.04.2 LTS (jammy) | 250GB SSD | arm64, 8 cores, 4GB RAM | Kubernetes cluster |
| k8s-03 | Orange Pi 5 | Ubuntu Server 22.04.2 LTS (jammy) | 250GB SSD | arm64, 8 cores, 16GB RAM | Kubernetes cluster |
| util-02 | Lenovo ThinkCentre M710Q | Ubuntu Server 22.04.2 LTS (jammy) | 128GB SSD, 1TB HDD | amd64, i5-7500, 4GB RAM (upgradeable) | Extra storage, such as NFS and Nexus |
