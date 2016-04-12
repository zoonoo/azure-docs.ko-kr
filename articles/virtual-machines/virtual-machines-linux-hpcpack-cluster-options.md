<properties
 pageTitle="클라우드의 Linux HPC Pack 클러스터 옵션 | Microsoft Azure"
 description="Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Linux HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다."
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Microsoft HPC Pack을 사용하여 Azure에서 Linux HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하는 옵션

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## Azure VM에서 HPC Pack 클러스터 실행

### Azure 템플릿


* (마켓플레이스) [Linux 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (퀵 스타트) [Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Azure VM 이미지

* [HPC Pack on Windows Server 2012 R2(영문)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### PowerShell 배포 스크립트

* [HPC Pack IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### 자습서

* [자습서: Azure에서 HPC Pack 클러스터의 Linux 컴퓨터 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)

* [자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)



### 클러스터 관리

* [Azure에서 HPC Pack 클러스터에 작업 제출](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)



## MPI 작업에 대한 RDMA 클러스터 만들기

* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->