<properties
 pageTitle="클라우드의 HPC Pack 클러스터 옵션 | Microsoft Azure"
 description="Microsoft HPC Pack을 사용하여 Azure 클라우드에서 HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다."
 services="virtual-machines,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Microsoft HPC Pack을 사용하여 Azure에서 HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하는 옵션

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서는 리소스 관리자 배포 모델 또는 클래식 배포 모델을 사용하여 리소스를 만드는 방법에 적용됩니다.

클라우드 기반 HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하려면 Microsoft HPC Pack과 Azure 계산 및 인프라 서비스를 이용합니다. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)은 Microsoft Azure 및 Windows Server 기술로 구축된 Microsoft의 무료 HPC 솔루션으로, Windows와 Linux HPC 작업 모두 지원합니다. 클라우드 기반 HPC Pack 클러스터는 클러스터 관리자 또는 ISV(Independent Software Vendor)에 유연하고 확장 가능한 플랫폼을 제공하여 온-프레미스 계산 클러스터 인프라에 대한 투자를 줄이면서 계산 집약적인 응용 프로그램을 실행합니다.

<!-- Take advantage of automated tools for HPC Pack cluster deployment in Azure VMs, including an HPC Pack image that can be used with either Azure quickstart templates or Azure PowerShell scripts, or deploy your cluster manually in the Azure portal.-->


## Azure VM에서 HPC Pack 클러스터 실행


### Azure VM 이미지

* [HPC Pack on Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)(영문)

* [HPC Pack compute node on Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)(영문)

* [HPC Pack compute node with Excel on Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)(영문)

### Azure 빠른 시작 템플릿

* [HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)(영문)

* [Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [사용자 지정 컴퓨터 노드 이미지로 HPC 클러스터 만들기](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### PowerShell 배포 스크립트

* [HPC Pack IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](virtual-machines-hpcpackcluster-powershell-script.md)

### 자습서

* [자습서: Azure에서 HPC Pack 클러스터의 Linux 컴퓨터 노드 시작](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [자습서: Azure에서 Excel 및 SOA 작업을 실행할 HPC Pack 클러스터 시작](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### Azure 포털을 사용하여 수동 배포



* [Azure VM에서 HPC Pack 클러스터의 헤드 노드 설정](virtual-machines-hpcpack-cluster-headnode.md)

### 클러스터 관리

* [Azure에서 HPC Pack 클러스터의 컴퓨터 노드 관리](virtual-machines-hpcpack-cluster-node-manage.md)

* [Azure에서 HPC Pack 헤드 노드에 Azure "버스트" 노드 추가](virtual-machines-hpcpack-cluster-node-burst.md)

* [HPC Pack 클러스터에서 Azure 계산 리소스 확장 및 축소](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Azure에서 HPC Pack 클러스터에 작업 제출](virtual-machines-hpcpack-cluster-submit-jobs.md)



## HPC Pack 클러스터에 작업자 역할 노드 추가


* [HPC 팩을 사용하여 Azure로 버스트](https://technet.microsoft.com/library/gg481749.aspx)

* [자습서: Azure에서 HPC 팩을 사용하여 하이브리드 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Azure에서 HPC Pack 헤드 노드에 Azure "버스트" 노드 추가](virtual-machines-hpcpack-cluster-node-burst.md)

* [HPC Pack 클러스터에서 Azure 계산 리소스 확장 및 축소](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)


## MPI 작업에 대한 RDMA 클러스터 만들기

* [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](virtual-machines-windows-hpcpack-cluster-rdma.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=Oct15_HO1-->