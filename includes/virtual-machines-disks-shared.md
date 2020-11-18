---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7831eaaa478a3f28ff158d5c9599abaf8a107c15
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684434"
---
Azure 공유 디스크는 여러 Vm (가상 컴퓨터)에 관리 디스크를 동시에 연결할 수 있도록 하는 Azure managed disks에 대 한 새로운 기능입니다. 관리 디스크를 여러 VM에 연결하면 새 클러스터된 애플리케이션을 배포하거나 기존 클러스터된 애플리케이션을 Azure로 마이그레이션할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

클러스터의 Vm은 scsi PR ( [Scsi 영구 예약](https://www.t10.org/members/w_spc3.htm) )을 사용 하 여 클러스터 된 응용 프로그램에서 선택한 예약에 따라 연결 된 디스크를 읽거나 쓸 수 있습니다. SCSI PR은 온-프레미스 SAN(저장 영역 네트워크)에서 실행되는 애플리케이션에서 활용하는 업계 표준입니다. 관리 디스크에서 SCSI PR을 사용하도록 설정하면 이러한 애플리케이션을 있는 그대로 Azure로 마이그레이션할 수 있습니다.

공유 관리 디스크는 여러 Vm에서 액세스할 수 있는 공유 블록 저장소를 제공 합니다. 이러한 저장소는 Lun (논리 단위 번호)으로 노출 됩니다. 이때 LUN은 대상(디스크)에서 개시 장치(VM)로 제공됩니다. 이러한 LUN은 VM에 대한 DAS(직접 연결 스토리지) 또는 로컬 드라이브처럼 보입니다.

공유 관리 디스크는 SMB/NFS를 사용하여 액세스할 수 있는 완전 관리형 파일 시스템을 기본적으로 제공하지 않습니다. 클러스터 노드 통신 및 쓰기 잠금을 처리 하는 WSFC (Windows Server 장애 조치 (Failover) 클러스터) 또는 Pacemaker와 같은 클러스터 관리자를 사용 해야 합니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>운영 체제 요구 사항

공유 디스크는 여러 운영 체제를 지원 합니다. 지원 되는 운영 체제는 [Windows](#windows) 또는 [Linux](#linux) 섹션을 참조 하십시오.

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>샘플 워크로드

### <a name="windows"></a>Windows

Azure 공유 디스크는 Windows Server 2008 이상에서 지원 됩니다. 대부분의 Windows 기반 클러스터링은 클러스터 노드 통신용 핵심 인프라를 모두 처리 하 여 응용 프로그램이 병렬 액세스 패턴을 활용할 수 있도록 하는 WSFC를 기반으로 합니다. WSFC를 사용하면 Windows Server 버전에 따라 CSV 기반 옵션과 CSV 기반이 아닌 옵션을 모두 사용할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 만들기](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)를 참조하세요.

WSFC에서 실행되는 인기 있는 일부 애플리케이션은 다음과 같습니다.

- [Azure 공유 디스크를 사용 하 여 FCI 만들기 (Azure Vm에서 SQL Server)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- SoFS (스케일 아웃 파일 서버) [템플릿] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [템플릿] (https://aka.ms/azure-shared-disk-sapacs-template)
- 일반적으로 사용되는 파일 서버(IW 워크로드)
- RDS UPD(원격 데스크톱 서버 사용자 프로필 디스크)

### <a name="linux"></a>Linux

Azure 공유 디스크는 다음에서 지원 됩니다.
- [SUSE SLE for SAP and SUSE SLE HA 15 SP1 이상](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18.04 이상](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL 8 버전의 RHEL developer preview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Linux 클러스터는 [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)와 같은 클러스터 관리자를 활용할 수 있습니다. Pacemaker는 [Corosync](http://corosync.github.io/corosync/)를 기반으로 하며, 고가용성 환경에 배포된 애플리케이션에 클러스터 통신을 사용하도록 설정합니다. 몇 가지 일반적인 클러스터된 파일 시스템으로는 [ocfs2](https://oss.oracle.com/projects/ocfs2/) 및 [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)가 있습니다. Scsi 영구 예약 (SCSI PR) 및/또는 STONITH SBD (Block Device) 기반 클러스터링 모델을 사용 하 여 디스크에 대 한 arbitrating 액세스를 수행할 수 있습니다. SCSI PR을 사용 하는 경우 [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) , [sg_persist](https://linux.die.net/man/8/sg_persist)등의 유틸리티를 사용 하 여 예약 및 등록을 조작할 수 있습니다.

## <a name="persistent-reservation-flow"></a>영구 예약 흐름

다음 다이어그램에서는 SCSI PR을 활용하여 한 노드에서 다른 노드로 장애 조치(failover)를 수행할 수 있는 2노드 클러스터된 샘플 데이터베이스 애플리케이션을 보여 줍니다.

![2노드 클러스터. 클러스터에서 실행 중인 애플리케이션이 디스크에 대한 액세스를 처리하고 있음](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

흐름은 다음과 같습니다.

1. Azure VM1 및 VM2 레지스터 둘 다에서 실행되는, 클러스터된 애플리케이션은 디스크에 대한 읽기 또는 쓰기 의도를 등록합니다.
1. 그러면 VM1의 애플리케이션 인스턴스가 디스크에 대한 독점 쓰기 예약을 받습니다.
1. 이 예약은 Azure 디스크에 적용되며, 이제 데이터베이스가 디스크에 독점적으로 데이터를 쓸 수 있습니다. VM2의 애플리케이션 인스턴스 쓰기가 실패하게 됩니다.
1. VM1의 애플리케이션 인스턴스가 중단되면 VM2의 인스턴스가 이제 데이터베이스 장애 조치(failover) 및 디스크 인계를 시작할 수 있습니다.
1. 이제 이 예약이 Azure 디스크에 적용되고 디스크가 더 이상 VM1의 쓰기를 허용하지 않으며 VM2의 쓰기만 허용합니다.
1. 클러스터된 애플리케이션은 데이터베이스 장애 조치(failover)를 완료하고 VM2의 요청을 처리할 수 있습니다.

다음 다이어그램은 기계 학습 모델 학습과 같은 병렬 프로세스를 실행하기 위해 디스크에서 데이터를 읽는 여러 노드로 구성된 다른 일반적인 클러스터된 워크로드를 보여 줍니다.

![4노드 VM 클러스터. 각 노드는 쓰기 의도를 등록하고, 애플리케이션은 쓰기 결과를 올바르게 처리하기 위해 독점 예약을 받음](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

흐름은 다음과 같습니다.

1. 모든 VM 레지스터에서 실행되는, 클러스터된 애플리케이션은 디스크에 대한 읽기 또는 쓰기 의도를 등록합니다.
1. VM1의 애플리케이션 인스턴스는 다른 VM에서 디스크에 대한 읽기를 시작할 때 디스크에 대한 독점 쓰기 예약을 받습니다.
1. 이 예약은 Azure 디스크에 적용됩니다.
1. 이제 클러스터의 모든 노드에서 디스크의 데이터를 읽을 수 있습니다. 클러스터의 모든 노드를 대표하는 하나의 노드만 결과를 디스크에 쓰기 저장합니다.

### <a name="ultra-disks-reservation-flow"></a>Ultra Disk 예약 흐름

Ultra Disk는 총 두 개의 제한에 대한 추가 제한을 제공합니다. 따라서 Ultra Disk 예약 흐름이 이전 섹션에 설명된 대로 작동하거나 성능을 제한하고 더 세부적으로 성능을 분산할 수 있습니다.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="예약 소유자, 등록 및 기타에 대 한 ' 읽기 전용 ' 또는 ' 읽기/쓰기 ' 액세스를 보여 주는 테이블의 이미지입니다.":::

## <a name="performance-throttles"></a>성능 제한

### <a name="premium-ssd-performance-throttles"></a>프리미엄 SSD performance 제한

프리미엄 SSD를 사용 하는 경우 디스크 IOPS 및 처리량은 고정 됩니다. 예를 들어 P30의 IOPS는 5000입니다. 이 값은 디스크가 2 개의 Vm 또는 5 개의 Vm에서 공유 되는지 여부를 유지 합니다. 단일 VM에서 디스크 제한에 도달 하거나 둘 이상의 Vm으로 나눌 수 있습니다. 

### <a name="ultra-disk-performance-throttles"></a>Ultra Disk 성능 제한

Ultra Disk는 수정 가능한 특성을 제공하고 수정할 수 있도록 하여 성능을 설정하는 고유한 기능을 제공합니다. 기본적으로 수정 가능한 특성은 두 개뿐이지만 공유 Ultra Disk에는 특성이 두 개 더 있습니다.


|attribute  |Description  |
|---------|---------|
|DiskIOPSReadWrite     |쓰기 액세스 권한이 있는 공유 디스크를 탑재하는 모든 VM에서 허용되는 총 IOPS 수         |
|DiskMBpsReadWrite     |쓰기 액세스 권한이 있는 공유 디스크를 탑재하는 모든 VM에서 허용되는 총 처리량(MB/s)         |
|DiskIOPSReadOnly*     |공유 디스크를 탑재 하는 모든 Vm에서 허용 되는 총 IOPS 수 `ReadOnly` 입니다.         |
|DiskMBpsReadOnly*     |공유 디스크를 탑재 하는 모든 Vm에서 허용 되는 총 처리량 (MB/s) `ReadOnly` 입니다.         |

\* 공유 Ultra Disk에만 적용

다음 수식에서는 사용자가 수정 가능한 성능 특성을 설정하는 방법을 설명합니다.

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - 300IOPS/GiB의 IOPS 제한(디스크당 최대 160K IOPS)
    - 100 IOPS의 최솟값
    - DiskIOPSReadWrite + DiskIOPSReadOnly가 2IOPS/GiB 이상
- DiskMBpsRead    Write/DiskMBpsReadOnly:
    - 단일 디스크의 처리량 한도는 프로비저닝된 IOPS당 256KiB/s이며, 디스크당 최대 2,000MBps입니다.
    - 디스크당 보장되는 최소 처리량은 프로비저닝된 각 IOPS당 4KiB/s이고, 전체 기준은 1MBps 이상입니다.

#### <a name="examples"></a>예

공유 Ultra Disk에서 제한이 작동하는 방법을 구체적으로 보여 주는 몇 가지 시나리오의 예는 다음과 같습니다.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>클러스터 공유 볼륨을 사용하는 2노드 클러스터

클러스터된 공유 볼륨을 사용하는 2노드 WSFC의 예는 다음과 같습니다. 이 구성을 사용 하는 경우 두 Vm은 디스크에 대 한 동시 쓰기 액세스 권한을 가지 며,이로 인해 `ReadWrite` 두 vm 간에 제한이 분할 되 고 `ReadOnly` 스로틀이 사용 되지 않습니다.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 2노드 울트라 예제":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>클러스터 공유 볼륨을 사용하지 않는 2노드 클러스터

다음은 클러스터된 공유 볼륨을 사용하지 않는 2노드 WSFC의 예입니다. 이 구성을 사용하면 디스크에 대한 쓰기 권한이 하나의 VM에만 있습니다. 이로 인해 제한이 `ReadWrite` 주 VM에 독점적으로 사용 되 고 보조 데이터베이스에만 제한이 사용 됩니다 `ReadOnly` .

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 2노드에 CSV Ultra Disk가 없는 예제":::

##### <a name="four-node-linux-cluster"></a>4노드 Linux 클러스터

단일 작성기 및 3개의 스케일 아웃 판독기를 사용하는 4노드 Linux 클러스터의 예는 다음과 같습니다. 이 구성을 사용하면 디스크에 대한 쓰기 권한이 하나의 VM에만 있습니다. 이렇게 하면 제한이 `ReadWrite` 주 vm에 독점적으로 사용 되며 `ReadOnly` 보조 vm에 의해 제한 됩니다.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="4노드 울트라 제한 예제":::

#### <a name="ultra-pricing"></a>울트라 가격

Ultra shared 디스크는 프로 비전 된 용량, 총 프로 비전 된 IOPS (diskIOPSReadWrite + diskIOPSReadOnly) 및 총 프로 비전 된 처리량 MBps (diskMBpsReadWrite + diskMBpsReadOnly)를 기준으로 가격이 책정 됩니다. 추가 VM 탑재에 대해 추가 요금이 부과 되지 않습니다. 예를 들어 다음 구성이 포함 된 ultra 공유 디스크 (diskSizeGB: 1024, DiskIOPSReadWrite: 1만, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1)는 두 개의 Vm 또는 5 개의 Vm에 탑재 되었는지 여부에 관계 없이 1024 GiB, 10100 IOPS 및 601 MBps로 청구 됩니다.