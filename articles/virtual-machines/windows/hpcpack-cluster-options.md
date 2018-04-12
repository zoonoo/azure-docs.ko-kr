---
title: Azure의 Windows HPC 팩 클러스터 옵션 | Microsoft Docs
description: Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Windows HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다.
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: c5b8c16e076be3002425ceeac377043cea1a40a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>HPC Pack을 사용하여 Azure에서 Windows HPC 워크로드의 클러스터를 만들고 관리하기 위한 옵션
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서는 Windows 워크로드를 실행하기 위한 HPC Pack 클러스터를 생성하는 옵션을 중점적으로 설명합니다. 또한 [Linux HPC 워크로드](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 실행하는 HPC 팩 클러스터를 생성하는 옵션도 있습니다.


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM의 HPC Pack 클러스터 및 VM 확장 집합
### <a name="azure-templates"></a>Azure 템플릿
* (GitHub) [HPC 팩 2016 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC 팩 2012 R2 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Windows 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Excel 워크로드용 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (퀵 스타트) [HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (퀵 스타트) [사용자 지정 계산 노드 이미지로 HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM 이미지
* [Windows Server 2016의 HPC Pack 2016 헤드 노드](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2016의 HPC Pack 2016 계산 노드](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Windows Server 2012 R2의 HPC Pack 2016 헤드 노드](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2의 HPC Pack 2016 계산 노드](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Windows Server 2012 R2의 HPC Pack 2012 R2 헤드 노드](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Windows Server 2012 R2의 HPC Pack 2012 R2 계산 노드](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [HPC Pack compute node with Excel on Windows Server 2012 R2(영문)](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>HPC Pack 2012 R2에 대한 PowerShell 배포 스크립트
* [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>자습서
* [자습서: Azure에서 HPC 팩 2016 클러스터 배포](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [자습서: Azure에서 Excel 및 SOA 워크로드를 실행할 HPC 팩 클러스터 시작](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Azure 포털을 사용하여 수동 배포
* [Azure VM에서 HPC 팩 클러스터의 헤드 노드 설정](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>클러스터 관리
* [Azure에서 HPC 팩 클러스터의 컴퓨터 노드 관리](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [HPC 팩 클러스터에서 Azure 계산 리소스 확장 및 축소](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Azure에서 HPC 팩 클러스터에 작업 제출](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack의 작업 관리](https://technet.microsoft.com/library/jj899585.aspx)
* [Azure에서 Azure Active Directory로 HPC Pack 2016 클러스터 관리](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>작업자 역할 노드를 사용하여 버스트 
* [HPC 팩을 사용한 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)
* [자습서: Azure에서 HPC 팩을 사용하여 하이브리드 클러스터 설정](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Azure에서 HPC 팩 헤드 노드에 Azure "버스트" 노드 추가](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Azure Batch를 사용하여 버스트
* [HPC 팩을 사용하여 Azure Batch로 버스트](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>MPI 워크로드에 대한 RDMA 클러스터
* [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

