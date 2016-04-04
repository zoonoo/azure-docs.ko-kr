<properties
 pageTitle="A8 - A11 인스턴스 및 Linux 정보 | Microsoft Azure"
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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Linux에서 A8, A9, A10 및 A11 계산 집약적 인스턴스 정보

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 네트워크에 액세스

단일 클라우드 서비스 또는 가용성 집합 내에서 A8 및 A9 인스턴스가 Linux MPI 응용 프로그램을 실행하기 위해 Azure의 RDMA 네트워크에 액세스할 수 있습니다. 현재 Azure Linux RDMA는 [Intel MPI Library 5](https://software.intel.com/ko-KR/intel-mpi-library/)만 지원합니다.

>[AZURE.NOTE] 현재 Azure Linux RDMA 드라이버는 드라이버 확장을 통한 설치에 사용할 수 없습니다. 오직 Azure Marketplace의 RDMA-enabled SLES 12 이미지를 사용하는 경우에만 사용 가능합니다.

다음 표에서 계산 노드의 클러스터(laaS)에서 RDMA 네트워크에 액세스하기 위해 Linux MPI 응용 프로그램에 대한 필수 조건을 요약합니다. 배포 옵션 및 구성 단계는 [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)을 참조하세요.

필수 요소 | 가상 컴퓨터(IaaS)
------------ | -------------
운영 체제 | Azure Marketplace의 SLES 12 HPC 이미지
MPI | Intel MPI Library 5

## HPC 팩 및 Linux에 대한 고려 사항

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Windows용 Microsoft의 무료 HPC 클러스터 및 작업 관리 솔루션입니다. HPC Pack 2012 R2의 최신 릴리스는 여러 Linux 배포를 지원하여 Windows Server 헤드 노드를 통해 관리되는 Azure VM에서 배포된 계산 노드에서 실행할 수 있습니다. A8 또는 A9 VM에 배포되고 지원되는 MPI 구현을 실행하는 Linux 계산 노드에서 RDMA 네트워크에 액세스하는 MPI 응용 프로그램을 실행할 수 있습니다. 시작하려면 [Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)을 참조하세요.

## 다음 단계

* A8, A9, A10 및 A11 인스턴스의 가용성 및 가격에 대한 세부 정보는 [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/)을 참조하세요.

* Linux에서 RDMA를 사용하여 A8 및 A9 인스턴스를 배포하고 사용하려면 [MPI 응용 프로그램을 실행하기 위해 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)을 참조하세요.

<!---HONumber=AcomDC_0323_2016-->