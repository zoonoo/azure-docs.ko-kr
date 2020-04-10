---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008554"
---
Azure 공유 디스크(미리 보기)는 관리디스크를 여러 가상 시스템(VM)에 동시에 연결할 수 있는 Azure 관리 디스크의 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 디스크를 배포하거나 기존 클러스터된 응용 프로그램을 Azure에 마이그레이션할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

클러스터의 VM은 [SCSI 영구 예약(SCSI](https://www.t10.org/members/w_spc3.htm) PR)을 사용하여 클러스터된 응용 프로그램에서 선택한 예약을 기반으로 연결된 디스크를 읽거나 쓸 수 있습니다. SCSI PR은 SAN(스토리지 영역 네트워크) 온-프레미스에서 실행되는 애플리케이션이 활용하는 업계 표준입니다. 관리 디스크에서 SCSI PR을 사용하도록 설정하면 이러한 응용 프로그램을 있는 그대로 Azure로 마이그레이션할 수 있습니다.

공유 관리 디스크는 여러 VM에서 액세스할 수 있는 공유 블록 저장소를 제공하며, 이러한 저장소는 논리 단위 번호(LuN)로 노출됩니다. 그런 다음 대상(디스크)에서 VM(이니시에이터)에 루넨이 표시됩니다. 이러한 Lun은 DAS(직접 연결 저장소) 또는 VM에 대한 로컬 드라이브처럼 보입니다.

공유 관리 디스크는 기본적으로 SMB/NFS를 사용하여 액세스할 수 있는 완전히 관리되는 파일 시스템을 제공하지 않습니다. 클러스터 노드 통신을 처리하고 쓰기 잠금을 처리하는 WSFC(Windows 서버 장애 조치 클러스터) 또는 Pacemaker와 같은 클러스터 관리자를 사용해야 합니다.

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

### <a name="ultra-disks-reservation-flow"></a>울트라 디스크 예약 흐름

울트라 디스크는 총 2개의 스로틀에 대해 추가 스로틀을 제공합니다. 이로 인해 울트라 디스크 예약 흐름은 이전 섹션에서 설명한 대로 작동하거나 성능을 보다 세밀하게 제한하고 배포할 수 있습니다.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>울트라 디스크 성능 제한

울트라 디스크에는 수정 가능한 특성을 노출하고 수정할 수 있도록 하여 성능을 설정할 수 있는 고유한 기능이 있습니다. 기본적으로 수정 가능한 속성은 두 개뿐이지만 공유 울트라 디스크에는 두 가지 추가 속성이 있습니다.


|attribute  |Description  |
|---------|---------|
|디스키오프스읽기쓰기     |쓰기 액세스 권한이 있는 공유 디스크를 탑재하는 모든 VM에서 허용되는 총 IOPS 수입니다.         |
|디스크MBps읽기쓰기     |모든 VM에서 허용되는 총 처리량(MB/s)은 쓰기 액세스 권한이 있는 공유 디스크를 탑재합니다.         |
|디스키오프스읽기전용*     |공유 디스크를 ReadOnly로 장착하는 모든 VM에서 허용되는 총 IOPS 수입니다.         |
|디스크MBps읽기전용*     |공유 디스크를 ReadOnly로 장착하는 모든 VM에서 허용되는 총 처리량(MB/s)입니다.         |

\*공유 울트라 디스크에만 적용됩니다.

다음 수식에서는 사용자가 수정할 수 있기 때문에 성능 특성을 설정하는 방법을 설명합니다.

- DiskIOPSReadWrite/DiskIOPS읽기전용: 
    - IOPS 제한 300 IOPS/GiB, 디스크당 최대 160K IOPS
    - 최소 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSRead만은 적어도 2 IOPS/GiB입니다.
- DiskMBps읽기 쓰기/디스크MBps읽기전용:
    - 단일 디스크의 처리량 제한은 프로비저닝된 각 IOPS에 대해 256 KiB/s이며 디스크당 최대 2000MBps입니다.
    - 디스크당 최소 보장 처리량은 프로비저닝된 각 IOPS에 대해 4KiB/s이며, 전체 기준최소 는 1MBps입니다.

### <a name="examples"></a>예

다음 예제에서는 제한이 공유 울트라 디스크에서 어떻게 작동하는지 보여 주는 몇 가지 시나리오를 보여 주며, 특히

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>클러스터 공유 볼륨을 사용하는 두 노드 클러스터

다음은 클러스터된 공유 볼륨을 사용하는 2노드 WSFC의 예입니다. 이 구성을 사용하면 두 VM모두 디스크에 대한 동시 쓰기 액세스 권한을 가지므로 ReadWrite 스로틀이 두 VM에 걸쳐 분할되고 ReadOnly 스로틀이 사용되지 않습니다.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 2노드 울트라 예제":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>클러스터 공유 볼륨이 없는 두 개의 노드 클러스터

다음은 클러스터된 공유 볼륨을 사용하지 않는 2노드 WSFC의 예입니다. 이 구성에서는 하나의 VM만 디스크에 대한 쓰기 액세스 권한을 가지게 됩니다. 이렇게 하면 ReadWrite 스로틀이 기본 VM에만 사용되고 ReadOnly 스로틀은 보조 에서만 사용됩니다.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 두 노드 없음 CSV 울트라 디스크 예제":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>4노드 리눅스 클러스터

다음은 단일 기록기와 3개의 스케일 아웃 판독기를 갖춘 4노드 Linux 클러스터의 예입니다. 이 구성에서는 하나의 VM만 디스크에 대한 쓰기 액세스 권한을 가지게 됩니다. 이렇게 하면 ReadWrite 스로틀이 기본 VM에만 사용되고 읽기 전용 스로틀이 보조 VM에 의해 분할됩니다.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="4개의 노드 울트라 스로틀링 예제":::

:::image-end:::