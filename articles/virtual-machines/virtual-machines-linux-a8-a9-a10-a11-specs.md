<properties
 pageTitle="Linux를 사용한 A8, A9, A10, A11 VM 크기 정보 | Microsoft Azure"
 description="Linux VM을 위한 Azure A8, A9, A10 및 A11 계산 집약적 크기 사용에 관한 배경 정보와 고려사항을 얻습니다."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/04/2016"
 ms.author="danlep"/>

# A8, A9, A10 및 A11 계산 집약적 인스턴스 정보 

여기에는 *계산 집약적* 인스턴스로 알려진 Azure A8, A9, A10 및 A11 인스턴스 사용에 대한 일부 고려사항과 배경 정보가 있습니다. 이 문서는 Linux VM에 대해 이러한 인스턴스를 사용하는 것이 중점을 두고 있습니다. 이 문서는 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md)에도 적용됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 네트워크에 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 다음 지원되는 Linux HPC 배포 및 지원되는 MPI 구현 중 하나를 실행하는 A8 및 A9 크기 Linux VM의 클러스터는 Azure의 RDMA 네트워크에 액세스하여 Linux MPI 응용 프로그램을 실행할 수 있습니다. 배포 옵션 및 샘플 구성 단계는 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)을 참조하세요.

* **배포** - Azure 마켓플레이스 이미지에서 배포된 12 HPC용 SLES(SUSE Linux Enterprise Server), 12 HPC용 SLES(Premium), CentOS 기반 7.1 HPC 또는 CentOS 기반 6.5 HPC입니다.

* **MPI** - Intel MPI Library 5

    >[AZURE.NOTE] Intel MPI 5.1은 마켓플레이스에 있는 CentOS 기반 HPC 이미지에 이미 설치되었습니다. SLES 12 HPC VM에서 Intel MPI를 사용하려면 별도로 설치해야 합니다.

현재 RDMA를 지원하는 SLES 12 HPC 및 Azure 마켓플레이스의 CentOS HPC 이미지를 배포하는 경우에만 Azure Linux RDMA 드라이버가 설치됩니다. 배포한 다른 Linux VM에 드라이버를 설치할 수 없습니다.

>[AZURE.NOTE]마켓플레이스의 CentOS 기반 HPC 이미지에서 커널 업데이트는 **yum** 구성 파일을 사용할 수 없습니다. 즉 Linux RDMA 드라이버가 RPM 패키지로 배포되기 때문에 커널이 업데이트되는 경우 드라이버 업데이트가 작동하지 않을 수 있습니다.


## SLES 12에 대한 RDMA 드라이버 업데이트
SLES 12 HPC 이미지에 따라 VM을 만든 후에 RDMA 네트워크 연결을 위해 VM에서 RDMA 드라이버를 업데이트해야 할 수 있습니다.

>[AZURE.IMPORTANT]이 단계는 모든 Azure 지역에서 SLES 12 HPC VM 배포에 **필요**합니다. CentOS 기반 7.1 HPC 또는 6.5 HPC VM을 배포한 경우 이 단계가 필요하지 않습니다.

드라이버를 업데이트 하기 전에 **zypper** 프로세스 또는 VM에서 SUSE 리포지토리 데이터베이스를 잠그는 모든 프로세스를 중지합니다. 그렇지 않으면 드라이버가 제대로 업데이트되지 않을 수 있습니다.

각 VM에서 Linux RDMA 드라이버를 업데이트하려면 클라이언트 컴퓨터에서 다음 Azure CLI 명령 집합 중 하나를 실행합니다.

**클래식 배포 모델에서 프로비전된 SLES 12 HPC VM의 경우**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**리소스 관리자 배포 모델에서 프로비전된 SLES 12 HPC VM의 경우**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]드라이버를 설치하는 데 약간의 시간이 걸릴 수 있으며 명령은 출력 없이 반환됩니다. 업데이트가 수행된 후 VM이 다시 시작하므로 몇 분 후에 사용할 수 있습니다.

### 드라이버 업데이트용 샘플 스크립트

SLES 12 HPC VM의 클러스터가 있는 경우 클러스터의 모든 노드에서 드라이버 업데이트를 스크립팅할 수 있습니다. 예를 들어 다음 스크립트는 8개 노드 클러스터에서 드라이버를 업데이트합니다.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## HPC 팩 및 Linux에 대한 고려 사항

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션입니다. HPC Pack 2012 R2의 최신 릴리스는 여러 Linux 배포를 지원하여 Windows Server 헤드 노드를 통해 관리되는 Azure VM에서 배포된 계산 노드에서 실행할 수 있습니다. A8 또는 A9 VM에 배포되고 지원되는 MPI 구현을 실행하는 Linux 계산 노드에서 RDMA 네트워크에 액세스하는 MPI 응용 프로그램을 실행할 수 있습니다. 시작하려면 [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)을 참조하세요.

## 네트워크 토폴로지 고려 사항

* Azure의 A8 또는 A9 크기 Linux VM에서 Eth1은 RDMA 네트워크 트래픽용으로 예약됩니다. Eth1 설정 또는 이 네트워크를 참조하는 구성 파일의 정보를 변경하지 마세요. Eth0은 일반 Azure 네트워크 트래픽용으로 예약됩니다.

* Azure IP over Infiniband(IB)는 지원되지 않습니다. RDMA over IB만 지원됩니다.


## 다음 단계

* A8, A9, A10 및 A11 인스턴스의 가용성 및 가격 책정에 대한 세부 정보는 [가상 컴퓨터 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)을 참조하세요.

* 저장소 용량 및 디스크 세부 정보는 [가상 컴퓨터 크기](virtual-machines-linux-sizes.md)를 참조하세요.

* Linux에서 RDMA를 사용하여 A8 및 A9 인스턴스를 배포하고 사용하려면 [MPI 응용 프로그램을 실행하기 위해 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)을 참조하세요.

<!---HONumber=AcomDC_0810_2016-->