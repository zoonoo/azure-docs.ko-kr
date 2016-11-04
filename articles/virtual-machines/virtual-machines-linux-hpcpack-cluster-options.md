---
title: 클라우드의 Linux HPC Pack 클러스터 옵션 | Microsoft Docs
description: Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Linux HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다.
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep

---
# HPC Pack을 사용하여 Azure에서 Linux 워크로드용 HPC 클러스터를 만들고 관리하기 위한 옵션
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서는 HPC 팩을 사용하여 Linux 워크로드를 실행할 때 사용 가능한 옵션에 대해 살펴봅니다. 또한 [HPC 팩을 사용하여 Windows HPC 워크로드](virtual-machines-windows-hpcpack-cluster-options.md)를 실행할 때 사용 가능한 옵션도 있습니다.

## Azure VM에서 HPC Pack 클러스터 실행
### Azure 템플릿
* (마켓플레이스) [Linux 워크로드용 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (퀵 스타트) [Linux 계산 노드가 포함된 HPC 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### PowerShell 배포 스크립트
* [HPC Pack IaaS 배포 스크립트를 사용하여 Linux HPC 클러스터 만들기](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### 자습서
* [자습서: Azure에서 HPC Pack 클러스터의 Linux 컴퓨터 노드 시작](virtual-machines-linux-classic-hpcpack-cluster.md)
* [자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩으로 STAR-CCM+ 실행](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### 클러스터 관리
* [Azure에서 HPC Pack 클러스터에 작업 제출](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
* [HPC Pack의 작업 관리](https://technet.microsoft.com/library/jj899585.aspx)

## MPI 작업에 대한 RDMA 클러스터 만들기
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->