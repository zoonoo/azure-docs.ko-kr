---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472022"
---
Azure 공유 디스크(미리 보기)는 Azure 관리 디스크를 여러 가상 시스템(VM)에 동시에 연결할 수 있는 Azure 관리 디스크의 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 디스크를 배포하거나 기존 클러스터된 응용 프로그램을 Azure에 마이그레이션할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

클러스터의 VM은 [SCSI 영구 예약(SCSI](https://www.t10.org/members/w_spc3.htm) PR)을 사용하여 클러스터된 응용 프로그램에서 선택한 예약을 기반으로 연결된 디스크를 읽거나 쓸 수 있습니다. SCSI PR은 SAN(스토리지 영역 네트워크) 온-프레미스에서 실행되는 애플리케이션이 활용하는 잘 알려진 업계 표준입니다. 관리 디스크에서 SCSI PR을 사용하도록 설정하면 이러한 응용 프로그램을 있는 그대로 Azure로 마이그레이션할 수 있습니다.

공유 디스크가 활성화된 관리형 디스크는 여러 VM에서 액세스할 수 있는 공유 블록 저장소를 제공하며, 이는 논리적 단위 번호(LuN)로 노출됩니다. 그런 다음 대상(디스크)에서 VM(이니시에이터)에 루넨이 표시됩니다. 이러한 Lun은 DAS(직접 연결 저장소) 또는 VM에 대한 로컬 드라이브처럼 보입니다.

공유 디스크가 활성화된 관리디스크는 SMB/NFS를 사용하여 액세스할 수 있는 완전히 관리되는 파일 시스템을 기본적으로 제공하지 않습니다. 클러스터 노드 통신을 처리하고 쓰기 잠금을 처리하는 WSFC(Windows 서버 장애 조치 클러스터) 또는 Pacemaker와 같은 클러스터 관리자를 사용해야 합니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>샘플 워크로드

### <a name="windows"></a>Windows

클러스터 노드 통신을 위한 모든 핵심 인프라를 처리하는 WSFC에서 대부분의 Windows 기반 클러스터링 빌드를 수행하므로 응용 프로그램이 병렬 액세스 패턴을 활용할 수 있습니다. WSFC는 Windows 서버 버전에 따라 CSV 및 비CSV 기반 옵션을 모두 사용할 수 있습니다. 자세한 내용은 [장애 조치 클러스터 만들기를](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)참조하십시오.

WSFC에서 실행되는 몇 가지 인기 있는 응용 프로그램은 다음과 같습니다.

- SQL 서버 장애 조치 클러스터 인스턴스(FCI)
- 파일 확장(SoFS)
- 일반 사용을 위한 파일 서버(IW 워크로드)
- 원격 데스크톱 서버 사용자 프로필 디스크(RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux 클러스터는 [Pacemaker와](https://wiki.clusterlabs.org/wiki/Pacemaker)같은 클러스터 관리자를 활용할 수 있습니다. Pacemaker는 [Corosync를](http://corosync.github.io/corosync/)기반으로 구축하여 가용성이 높은 환경에 배포된 애플리케이션을 위한 클러스터 통신을 지원합니다. 일부 일반적인 클러스터된 파일 시스템에는 [ocfs2](https://oss.oracle.com/projects/ocfs2/) 및 [gfs2가](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)포함됩니다. [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) 및 sg_persist 등의 유틸리티를 사용하여 예약 및 등록을 조작할 수 [있습니다.](https://linux.die.net/man/8/sg_persist)

## <a name="persistent-reservation-flow"></a>영구 예약 흐름

다음 다이어그램은 SCSI PR을 활용하여 한 노드에서 다른 노드로 장애 조치(failover)를 활성화하는 샘플 2노드 클러스터된 데이터베이스 응용 프로그램을 보여 줍니다.

![두 노드 클러스터. 클러스터에서 실행 중인 응용 프로그램이 디스크에 대한 액세스를 처리하고 있습니다.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

흐름은 다음과 같습니다.

1. Azure VM1 및 VM2에서 실행되는 클러스터된 응용 프로그램은 디스크를 읽거나 쓰려는 의도를 등록합니다.
1. 그런 다음 VM1의 응용 프로그램 인스턴스는 단독 예약을 사용하여 디스크에 씁니다.
1. 이 예약은 Azure 디스크에 적용되며 데이터베이스는 이제 디스크에 단독으로 쓸 수 있습니다. VM2의 응용 프로그램 인스턴스에서 작성하는 모든 쓰기는 성공하지 않습니다.
1. VM1의 응용 프로그램 인스턴스가 다운되면 VM2의 인스턴스가 이제 데이터베이스 장애 조치(failover)를 시작하고 디스크를 인수할 수 있습니다.
1. 이 예약은 이제 Azure 디스크에 적용되며 디스크는 더 이상 VM1의 쓰기를 허용하지 않습니다. VM2의 쓰기만 허용됩니다.
1. 클러스터된 응용 프로그램은 데이터베이스 장애 조치(failover)를 완료하고 VM2의 요청을 제공할 수 있습니다.

다음 다이어그램은 기계 학습 모델 교육과 같은 병렬 프로세스를 실행하기 위해 디스크에서 데이터를 읽는 여러 노드로 구성된 또 다른 일반적인 클러스터된 워크로드를 보여 줍니다.

![4 개의 노드 VM 클러스터, 각 노드는 쓰기 의도를 등록하고 응용 프로그램은 쓰기 결과를 올바르게 처리하기 위해 독점 예약을 합니다.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

흐름은 다음과 같습니다.

1. 모든 VM에서 실행되는 클러스터된 응용 프로그램은 디스크를 읽거나 쓰려는 의도를 등록합니다.
1. VM1의 응용 프로그램 인스턴스는 다른 VM에서 디스크에 읽기를 여는 동안 디스크에 쓰기 위해 단독 예약을 합니다.
1. 이 예약은 Azure 디스크에 적용됩니다.
1. 이제 클러스터의 모든 노드를 디스크에서 읽을 수 있습니다. 클러스터의 모든 노드를 대신하여 하나의 노드만 결과를 디스크에 다시 씁니다.