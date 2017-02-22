---
title: "클라우드의 Linux HPC 팩 클러스터 옵션 | Microsoft Docs"
description: "Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Linux HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다."
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 7d153f1f45eb5cb06d7b960e7449450b4c74747a
ms.openlocfilehash: e6c1fcea6ba7f6865cab97be9e3431bec2184064


---
# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>HPC Pack을 사용하여 Azure에서 Linux 워크로드용 HPC 클러스터를 만들고 관리하기 위한 옵션
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서는 HPC 팩을 사용하여 Linux 워크로드를 실행할 때 사용 가능한 옵션에 대해 살펴봅니다. 또한 [HPC 팩을 사용하여 Windows HPC 워크로드](virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 실행할 때 사용 가능한 옵션도 있습니다.

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Azure VM에서 HPC Pack 클러스터 실행
### <a name="azure-templates"></a>Azure 템플릿
* (GitHub) [HPC 팩 2016 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2016)
* (마켓플레이스) [Linux 워크로드용 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (퀵 스타트) [Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script"></a>PowerShell 배포 스크립트
* [HPC 팩 IaaS 배포 스크립트를 사용하여 Linux HPC 클러스터 만들기](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>자습서
* [자습서: Azure에서 HPC 팩 클러스터의 Linux 컴퓨터 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩으로 STAR-CCM+ 실행](virtual-machines-linux-classic-hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>클러스터 관리
* [Azure에서 HPC 팩 클러스터에 작업 제출](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack의 작업 관리](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>MPI 작업에 대한 RDMA 클러스터 만들기
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Feb17_HO1-->


