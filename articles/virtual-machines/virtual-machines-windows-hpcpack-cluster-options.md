<properties
 pageTitle="클라우드의 Windows HPC 팩 클러스터 옵션 | Microsoft Azure"
 description="Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Windows HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다."
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# HPC Pack을 사용하여 Azure에서 Windows HPC 클러스터를 만들고 관리하기 위한 옵션

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서는 Windows 워크로드를 실행하기 위한 HPC Pack 클러스터를 생성하는 옵션을 중점적으로 설명합니다. 또한 [HPC Pack을 사용하여 Linux HPC 워크로드](virtual-machines-linux-hpcpack-cluster-options.md)를 실행하는 클러스터를 생성하는 옵션도 있습니다.


## Azure VM에서 HPC Pack 클러스터 실행

### Azure 템플릿

* (마켓플레이스) [Windows 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (마켓플레이스) [Excel 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (퀵 스타트) [HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (퀵 스타트) [사용자 지정 계산 노드 이미지로 HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### Azure VM 이미지

* [HPC Pack on Windows Server 2012 R2(영문)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [HPC Pack compute node on Windows Server 2012 R2(영문)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [HPC Pack compute node with Excel on Windows Server 2012 R2(영문)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### PowerShell 배포 스크립트

* [HPC Pack IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### 자습서

* [자습서: Azure에서 Excel 및 SOA 작업을 실행할 HPC Pack 클러스터 시작](virtual-machines-windows-excel-cluster-hpcpack.md)



### Azure 포털을 사용하여 수동 배포

* [Azure VM에서 HPC Pack 클러스터의 헤드 노드 설정](virtual-machines-windows-hpcpack-cluster-headnode.md)

### 클러스터 관리

* [Azure에서 HPC Pack 클러스터의 컴퓨터 노드 관리](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [HPC Pack 클러스터에서 Azure 계산 리소스 확장 및 축소](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Azure에서 HPC Pack 클러스터에 작업 제출](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [HPC Pack의 작업 관리](https://technet.microsoft.com/library/jj899585.aspx)


## HPC Pack 클러스터에 작업자 역할 노드 추가


* [HPC 팩을 사용한 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)

* [자습서: Azure에서 HPC 팩을 사용하여 하이브리드 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Azure에서 HPC Pack 헤드 노드에 Azure "버스트" 노드 추가](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## Azure 배치와의 통합 

* [HPC 팩을 사용하여 Azure 배치로 버스트](https://technet.microsoft.com/library/mt612877.aspx)

## MPI 작업에 대한 RDMA 클러스터 만들기

* [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->