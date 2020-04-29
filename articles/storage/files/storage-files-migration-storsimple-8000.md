---
title: Azure File Sync로 StorSimple 8000 시리즈 마이그레이션
description: Azure File Sync로 StorSimple 8100 또는 8600 어플라이언스를 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755037"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Azure File Sync로 StorSimple 8100 및 8600 마이그레이션

StorSimple 8000 시리즈는 8100 또는 8600 물리적, 온-프레미스 어플라이언스 및 해당 클라우드 서비스 구성 요소로 표시 됩니다. 이러한 어플라이언스의 데이터를 Azure File Sync 환경으로 마이그레이션할 수 있습니다. Azure File Sync는 StorSimple 어플라이언스를 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 8000 시리즈는 12 월 2022에 [수명 종료](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 에 도달 합니다. 가능한 한 빨리 마이그레이션 계획을 시작 하는 것이 중요 합니다. 이 문서에서는 성공적인 마이그레이션에 대 한 필요한 배경 지식 및 마이그레이션 단계를 Azure File Sync으로 제공 합니다. 

## <a name="azure-file-sync"></a>Azure 파일 동기화

> [!IMPORTANT]
> Microsoft는 마이그레이션하는 고객을 지원 하기 위해 최선을 다하고 있습니다. 마이그레이션 AzureFilesMigration@microsoft 중에 지원 뿐만 아니라 사용자 지정 된 마이그레이션 계획에 대 한 Email.

Azure File Sync는 다음과 같은 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 파일 동기화 및 클라우드 계층화.
* 파일 공유는 SMB 및 파일 REST와 같은 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 저장소로 공유 됩니다. Azure 파일 공유는 기본적으로 네트워크 드라이브로 탑재할 수 있는 Windows 서버의 파일 공유와 비교할 수 있습니다. 특성, 권한 및 타임 스탬프와 같은 중요 한 파일 충실도 측면을 지원 합니다. Azure 파일 공유를 사용 하면 응용 프로그램 또는 서비스에서 클라우드에 저장 된 파일 및 폴더를 해석할 필요가 없습니다. Windows 파일 탐색기와 같은 친숙 한 프로토콜 및 클라이언트를 통해 기본적으로 액세스할 수 있습니다. Azure 파일을 공유 하는 것이 이상적인 방법 이며, 일부 응용 프로그램 데이터 뿐만 아니라 클라우드에서 범용 파일 서버 데이터를 저장 하는 가장 유연한 방법이 있습니다.

이 문서에서는 마이그레이션 단계를 중점적으로 설명 합니다. 마이그레이션하기 전에 Azure File Sync에 대해 자세히 알아보려면 다음 문서를 수행 하는 것이 좋습니다.

* [Azure File Sync-개요](https://aka.ms/AFS "개요")
* [Azure File Sync-배포 가이드](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>마이그레이션 목표

목표는 가용성 보장 뿐만 아니라 프로덕션 데이터의 무결성을 보장 하는 것입니다. 후자를 사용 하려면 최소 가동 중지 시간을 유지 하 여 정기적인 유지 관리 기간에만 또는 약간 초과할 수 있도록 해야 합니다.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Azure File Sync에 대 한 StorSimple 8000 시리즈 마이그레이션 경로

Azure File Sync 에이전트를 실행 하려면 로컬 Windows 서버가 필요 합니다. Windows Server는 최소 2012R2 서버에 있을 수 있지만 Windows Server 2019입니다.

다 수의 대체 마이그레이션 경로가 있으며,이를 통해 모든 항목을 문서화 하 고,이 문서에서 모범 사례로 인해 발생 하는 경로에 대 한 위험 또는 단점을 보여 주는 이유를 설명 하는 문서가 너무 깁니다.

![StorSimple 8000 시리즈 마이그레이션 단계 개요](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 시리즈 마이그레이션 경로이 문서의 아래에 있는 단계에 대 한 개요입니다.")

이전 이미지는이 문서의 섹션에 해당 하는 단계를 보여 줍니다.
클라우드 쪽 마이그레이션을 사용 하 여 로컬 StorSimple 어플라이언스에 대 한 불필요 한 파일 회수를 방지 합니다. 이 방법은 프로덕션 작업에 영향을 줄 수 있는 로컬 캐싱 동작 또는 네트워크 대역폭 사용에 영향을 주지 않습니다.
클라우드 쪽 마이그레이션은 데이터의 스냅숏 (볼륨 클론)에서 작동 합니다. 따라서 프로덕션 데이터는 마이그레이션의 끝에 나올 때까지이 프로세스에서 격리 됩니다. 기본적으로 백업 하는 작업을 수행 하면 모든 문제가 발생 하는 경우 마이그레이션을 안전 하 고 쉽게 반복할 수 있습니다.

## <a name="considerations-around-existing-storsimple-backups"></a>기존 StorSimple 백업 관련 고려 사항

StorSimple을 사용 하면 볼륨 클론의 형태로 백업을 수행할 수 있습니다. 이 문서에서는 새 볼륨 클론을 사용 하 여 라이브 파일을 마이그레이션합니다.
라이브 데이터 외에도 백업을 마이그레이션해야 하는 경우에도이 문서의 모든 지침이 적용 됩니다. 유일한 차이점은 새 볼륨 클론으로 시작 하는 대신 마이그레이션해야 하는 가장 오래 된 백업 볼륨 클론부터 시작 한다는 것입니다.

순서는 다음과 같습니다.

* 마이그레이션해야 하는 볼륨 클론의 최소 집합을 결정 합니다. 더 오래 마이그레이션하는 전체 마이그레이션 프로세스에서 더 오래 마이그레이션하는 백업을 더 많이 수행 하므로 가능한 경우이 목록을 최소한으로 유지 하는 것이 좋습니다.
* 마이그레이션 프로세스를 진행 하는 경우 마이그레이션하려는 가장 오래 된 볼륨 클론부터 시작 하 여 각 후속 마이그레이션에 대해 다음 가장 오래 된를 사용 합니다.
* 각 볼륨 복제 마이그레이션이 완료 되 면 Azure 파일 공유 스냅숏을 사용 해야 합니다. Azure [파일 공유 스냅숏은](storage-snapshots-files.md) azure 파일 공유에 대 한 파일 및 폴더 구조의 특정 시점 백업을 유지 하는 방법입니다. 마이그레이션을 진행 하는 동안 각 볼륨 클론의 버전을 보존 하려면 마이그레이션이 완료 된 후 이러한 스냅숏이 필요 합니다.
* 동일한 StorSimple 볼륨에서 제공 하는 모든 Azure 파일 공유에 대해 Azure 파일 공유 스냅숏을 사용 하도록 합니다. 볼륨 클론은 볼륨 수준에 있으며, Azure 파일 공유 스냅숏은 공유 수준에 있습니다. 볼륨 클론의 마이그레이션이 완료 된 후에는 각 Azure 파일 공유에 대 한 공유 스냅숏을 만들어야 합니다.
* 볼륨 클론에 대 한 마이그레이션 프로세스를 반복 하 고 각 볼륨 클론 후에 라이브 데이터의 스냅숏을 검색할 때까지 공유 스냅숏을 만듭니다. 볼륨 클론을 마이그레이션하는 프로세스는 아래 단계에 설명 되어 있습니다. 

백업을 이동 하지 않아도 되며, 라이브 데이터만 마이그레이션한 후에 Azure 파일 공유에서 새로운 백업 체인을 시작할 수 있는 경우 마이그레이션의 복잡성과 마이그레이션에 소요 되는 시간을 줄이는 데 도움이 됩니다. 백업을 이동할지 여부와 StorSimple에서 보유 한 각 볼륨 (각 공유 아님)의 수를 결정할 수 있습니다.

## <a name="phase-1-get-ready"></a>1 단계: 준비

:::row:::
    :::column:::
        ![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        마이그레이션의 기반은 볼륨 클론 및 가상 클라우드 어플라이언스 (StorSimple 8020 이라고 함)입니다.
이 단계에서는 Azure에서 이러한 리소스를 배포 하는 방법을 집중적으로 설명 합니다.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>StorSimple 8020 가상 어플라이언스 배포

클라우드 어플라이언스를 배포 하는 프로세스는 보안, 네트워킹 및 기타 몇 가지 고려 사항을 필요로 하는 프로세스입니다.

> [!IMPORTANT]
> 다음 가이드에는 일부 불필요 한 섹션이 포함 되어 있습니다. 처음부터 "3 단계"까지 문서를 읽고 따르세요. 그런 다음이 문서로 돌아옵니다. 이번에는이 가이드에서 "3 단계" 또는 그 외의 모든 항목을 완료 하지 않아도 됩니다.

[StorSimple 8020 가상 어플라이언스 배포](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>사용할 볼륨 클론 결정

마이그레이션을 시작할 준비가 되 면 첫 번째 단계는 StorSimple 클라우드 저장소의 현재 상태를 캡처하는 백업과 마찬가지로 새 볼륨 클론을 사용 하는 것입니다. 보유 한 각 StorSimple 볼륨에 대해 클론을 사용 합니다.
백업을 이동 해야 하는 경우 사용 하는 첫 번째 볼륨 클론은 새로 만든 클론은 아니지만 마이그레이션해야 하는 가장 오래 된 볼륨 클론 (가장 오래 된 백업)입니다.
자세한 지침은 ["기존 StorSimple 백업 관련 고려 사항"](#considerations-around-existing-storsimple-backups) 섹션을 참조 하세요.

> [!IMPORTANT]
> 다음 가이드에는 일부 불필요 한 섹션이 포함 되어 있습니다. 연결 대상 섹션에 설명 된 단계만 읽고 따릅니다. 그런 다음이 문서로 돌아옵니다. "다음 단계" 섹션을 따르지 않아도 됩니다.

[볼륨의 클론 만들기](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>볼륨 클론 사용

1 단계의 마지막 단계는 선택한 볼륨 클론을 Azure의 8020 가상 어플라이언스에서 사용할 수 있도록 만드는 것입니다.

> [!IMPORTANT]
> 다음 가이드는 필요한 단계를 포함 하지만 볼륨을 포맷 하는 명령 끝에도 있습니다. **볼륨 포맷 안 함** 시작 하기 전 명령: "10에서 연결 된" 섹션 7 "을 읽고 따릅니다. 단순 볼륨을 포맷 하려면 ... "  이 단계를 수행 하기 전에 중지 하 고이 문서로 돌아옵니다.

[Azure에서 8020 가상 어플라이언스에 볼륨 클론 탑재](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>1 단계 요약

1 단계를 완료 했으므로 다음 작업을 수행 합니다.

* Azure에서 StorSimple 8020 가상 어플라이언스를 배포 했습니다.
* 마이그레이션을 시작 하는 볼륨 클론을 결정 합니다.
* 사용 가능한 데이터를 사용 하 여 Azure의 StorSimple 가상 어플라이언스에 볼륨 클론 (각 라이브 볼륨에 하나씩)을 탑재 했습니다.

## <a name="phase-2-cloud-vm"></a>2 단계: 클라우드 VM

:::row:::
    :::column:::
        ![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Azure의 StorSimple 8020 가상 어플라이언스에서 초기 복제본을 사용할 수 있게 되 면 이제 VM을 프로 비전 하 고 iSCSI를 통해 해당 VM에 볼륨 클론 (또는 여러 개)을 노출 합니다.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Azure VM 배포

Azure의 Windows Server 가상 머신은 마이그레이션하는 동안에만 필요한 인프라의 임시 일부인 StorSimple 8020와 동일 합니다.
배포 하는 VM의 구성은 대부분 동기화 할 항목 (파일 및 폴더)의 수에 따라 달라 집니다. 문제가 있는 경우 더 높은 성능 구성으로 이동 하는 것이 좋습니다.

단일 Windows 서버에서 최대 30 개의 Azure 파일 공유를 동기화 할 수 있습니다.
모든 공유/경로 또는 StorSimple 볼륨의 루트를 포함 해야 하는 사양을 결정 하 고 항목 (파일 및 폴더) 수를 계산 합니다.

데이터의 전체 크기는 병목 현상이 낮습니다. 컴퓨터 사양에 맞게 조정 하는 데 필요한 항목의 수입니다.

* [동기화 해야 하는 항목 (파일 및 폴더)의 수를 기준으로 Windows Server 크기를 조정 하는 방법에 대해 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

    **다음 사항에 유의 하세요.** 이전에 연결 된 문서는 서버 메모리 (RAM) 범위를 포함 하는 테이블을 제공 합니다. Azure VM에 대해 많은 수의 방향으로 전환 합니다. 온-프레미스 컴퓨터에 대해 더 작은 수의 방향으로 지정할 수 있습니다.

* [Windows Server VM을 배포 하는 방법에 대해 알아봅니다.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> VM이 StorSimple 8020 가상 어플라이언스와 동일한 Azure 지역에 배포 되어 있는지 확인 합니다. 이 마이그레이션의 일환으로, 현재 Azure 파일 공유를 프로 비전 할 때 이후 단계에서 클라우드 데이터의 영역을 변경 해야 할 수도 있습니다.

> [!IMPORTANT]
> 온-프레미스 Windows Server를 사용 하 여 온-프레미스 StorSimple 어플라이언스를 전면으로 사용 하는 경우가 많습니다. 이러한 구성에서는 해당 Windows 서버에서 "[데이터 중복 제거](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" 기능을 사용 하도록 설정할 수 있습니다. **StorSimple 데이터에 데이터 중복 제거를 사용한 경우에도이 Azure VM에서 데이터 중복 제거를 사용 하도록 설정 해야 합니다.** 이 파일 수준 중복 제거를 StorSimples 기본 제공 블록 수준 중복 제거와 혼동 하지 마세요 .이 경우에는 작업이 필요 하지 않습니다.

> [!IMPORTANT]
> 성능을 최적화 하려면 클라우드 VM에 대 한 **빠른 OS 디스크** 를 배포 합니다. 모든 데이터 볼륨의 OS 디스크에 동기화 데이터베이스를 저장 합니다. 또한, **대량 OS 디스크**를 만들어야 합니다. StorSimple 볼륨의 항목 수 (파일 및 폴더)에 따라 OS 디스크에 동기화 데이터베이스를 수용 하기 위해 **몇 백 GiB** 의 공간이 필요할 수 있습니다.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Azure VM에 StorSimple 8020 볼륨 노출

이 단계에서는 iSCSI를 통해 8020 가상 어플라이언스에서 프로 비전 한 Windows Server VM으로 하나 이상의 StorSimple 볼륨을 연결 합니다.

> [!IMPORTANT]
> 다음 문서에서는 **클라우드 어플라이언스에 대 한 개인 IP 가져오기 및 iSCSI에 대 한** **연결** 섹션을 완료 하 고이 문서로 돌아옵니다.

1. [클라우드 어플라이언스에 대한 프라이빗 IP 가져오기](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [ISCSI를 통한 연결](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>2 단계 요약

이제 2 단계를 완료 했으므로 다음을 수행 했습니다. 

* 8020 가상 StorSimple 어플라이언스와 동일한 지역에 Windows Server VM을 프로 비전 합니다.
* ISCSI를 통해 8020에서 Windows Server VM으로 적용 가능한 모든 볼륨을 노출 합니다.
* 이제 탑재 된 볼륨의 서버 VM에서 파일 탐색기를 사용 하는 경우 파일 및 폴더 콘텐츠가 표시 됩니다.

마이그레이션이 필요한 모든 볼륨에 대해 이러한 단계를 완료 한 경우에만 3 단계로 진행 합니다.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>3 단계: Azure 파일 공유를 설정 하 고 Azure File Sync 준비

:::row:::
    :::column:::
        ![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        이 단계에서는 다양 한 Azure 파일 공유를 결정 하 고 프로 비전 하며, Windows Server 온-프레미스를 StorSimple 어플라이언스 교체로 만들고 Azure File Sync에 대해 해당 서버를 구성 합니다. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Azure 파일 공유에 기존 네임 스페이스 매핑

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure 파일 공유 배포

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> StorSimple 데이터가 있는 현재 지역에서 Azure 지역을 변경 해야 하는 경우 사용 하려는 새 지역에 Azure 파일 공유를 프로 비전 합니다. Azure 파일 공유를 포함 하는 저장소 계정을 프로 비전 할 때 지역을 선택 하 여 확인 합니다. 아래에서 프로 비전 할 Azure File Sync 리소스도 동일한 새 지역에 있는지 확인 합니다.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure File Sync 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> StorSimple 데이터가 있는 현재 지역에서 Azure 지역을 변경 해야 하는 경우 새 지역에서 Azure 파일 공유에 대 한 저장소 계정을 프로 비전 했습니다. 이 저장소 동기화 서비스를 배포할 때 동일한 지역을 선택 했는지 확인 합니다.

### <a name="deploy-an-on-premises-windows-server"></a>온-프레미스 Windows Server 배포

* 최소 2012R2-가상 머신 또는 물리적 서버로 Windows Server 2019을 만듭니다. Windows Server 장애 조치 (failover) 클러스터도 지원 됩니다. StorSimple 8100 또는 8600에 앞단에 된 서버를 다시 사용 하지 마세요.
* 직접 연결 된 저장소 (지원 되지 않는 NAS와 비교 하 여 DAS)를 프로 비전 하거나 추가 합니다.

StorSimple 8100 또는 8600 어플라이언스에서 로컬 캐싱에 사용할 수 있는 것 보다 새 Windows Server에 같거나 더 많은 저장소를 제공 하는 것이 가장 좋습니다. StorSimple 어플라이언스를 사용 하는 것과 같은 방식으로 Windows Server를 사용 합니다. 어플라이언스와 동일한 양의 저장소를 사용 하는 경우 캐싱 환경은 동일 하지 않은 경우 유사 해야 합니다.
Windows 서버에서 저장소를 추가 하거나 제거할 수 있습니다. 이렇게 하면 로컬 볼륨 크기와 캐싱에 사용할 수 있는 로컬 저장소의 크기를 조정할 수 있습니다.

### <a name="prepare-the-windows-server-for-file-sync"></a>파일 동기화를 위해 Windows Server 준비

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Windows Server에서 Azure File Sync 구성

이 프로세스를 위해 등록 된 온-프레미스 Windows Server가 준비 되어 있고 인터넷에 연결 되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **클라우드 계층화를 켜야 합니다.** 클라우드 계층화는 로컬 서버에서 클라우드에 저장 된 것 보다 저장소 용량을 줄일 수 있도록 하는 AFS 기능이 며, 전체 네임 스페이스를 사용할 수 있습니다. 로컬에서 흥미로운 데이터는 빠른 로컬 액세스 성능을 위해 로컬에도 캐시 됩니다. 이 단계에서 클라우드 계층화를 켜는 또 다른 이유는이 단계에서 파일 콘텐츠를 동기화 하지 않기 때문입니다 .이 경우에는 네임 스페이스만 이동 해야 합니다.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>4 단계: Azure VM의 동기화 구성

:::row:::
    :::column:::
        ![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        이 단계에서는 iSCSI 탑재 된 첫 번째 볼륨 클론을 사용 하 여 Azure VM에 대해 다룹니다. 이 단계에서는 Azure File Sync을 통해 VM을 연결 하 고 StorSimple 볼륨 클론에서 파일을 처음으로 이동 하는 작업을 시작 합니다.
        
    :::column-end:::
:::row-end:::

Azure File Sync에 대해 StorSimple 8100 또는 8600 어플라이언스를 대체할 온-프레미스 서버를 이미 구성 했습니다. 

Azure VM을 구성 하는 과정은 한 번의 추가 단계로 거의 동일 합니다. 다음 단계는이 과정을 안내 합니다.

> [!IMPORTANT]
> **클라우드 계층화를 사용 하도록 설정 하 여** Azure VM을 구성 하지 않는 것이 중요 합니다. 마이그레이션하는 동안이 서버의 볼륨을 최신 볼륨 복제로 교환 합니다. 클라우드 계층화에는 피해 야 하는 CPU 사용량에 대 한 이점과 오버 헤드가 없습니다.

1. [AFS 에이전트를 배포 합니다. (이전 섹션 참조)](#prepare-the-windows-server-for-file-sync)
2. [VM을 Azure File Sync 준비 하는 중입니다.](#get-the-vm-ready-for-azure-file-sync)
3. [동기화 구성](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>VM Azure File Sync 준비

Azure File Sync은 탑재 된 iSCSI StorSimple 볼륨에서 대상 Azure 파일 공유로 파일을 이동 하는 데 사용 됩니다.
이 마이그레이션 프로세스를 진행 하는 동안 여러 볼륨 클론을 동일한 드라이브 문자 아래의 VM에 탑재 합니다. 최신 버전의 파일 및 폴더로 탑재 한 다음 볼륨 클론을 표시 하 고 Azure File Sync를 통해 연결 된 Azure 파일 공유를 업데이트 하도록 Azure File Sync를 구성 해야 합니다. 

> [!IMPORTANT]
> 이렇게 하려면 Azure File Sync를 구성 하기 전에 서버에서 레지스트리 키를 설정 해야 합니다.

1. VM의 시스템 드라이브에 새 디렉터리를 만듭니다. 탑재 된 볼륨 클론 대신 Azure File Sync 정보를 유지 해야 합니다. `"C:\syncmetadata"`
2. Regedit를 열고 다음 레지스트리 하이브를 찾습니다.`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. String 형식의 새 키를 만듭니다. ***MetadataRootPath***
4. 시스템 볼륨에서 만든 디렉터리에 대 한 전체 경로를 설정 합니다. 예를 들면 다음과 같습니다.`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure VM에서 Azure File Sync 구성

이 단계는 온-프레미스 서버에서 AFS를 구성 하는 방법에 대해 설명 하는 이전 섹션과 비슷합니다.

차이점은이 서버에서 클라우드 계층화를 사용 하도록 설정 하면 안 되며 올바른 폴더가 올바른 Azure 파일 공유에 연결 되어 있는지 확인 해야 한다는 것입니다. 그렇지 않으면 Azure 파일 공유 및 데이터 콘텐츠 이름이 일치 하지 않으며 동기화를 다시 구성 하지 않고 클라우드 리소스 또는 로컬 폴더의 이름을 바꿀 방법이 없습니다.

[Windows Server에서 Azure File Sync를 구성 하는 방법에 대 한 이전 섹션](#configure-azure-file-sync-on-the-windows-server)을 참조 하세요.

### <a name="step-4-summary"></a>4 단계 요약

이 시점에서 iSCSI를 통해 StorSimple 볼륨 클론을 탑재 한 Azure VM에서 Azure File Sync 성공적으로 구성 된 것입니다.
이제 데이터는 Azure VM에서 다양 한 Azure 파일 공유로 이동 하 고, 온-프레미스 Windows Server에는 완전히 지 원하는 네임 스페이스가 표시 됩니다.

> [!IMPORTANT]
> 현재 Windows Server에 대 한 변경 또는 사용자 액세스 권한이 부여 되지 않았는지 확인 합니다.

Azure VM을 통해 Azure 파일 공유로 이동 하는 초기 볼륨 복제 데이터는 오랜 시간, 몇 주가 걸릴 수 있습니다. 이를 수행 하는 데 걸리는 시간을 예측 하는 것은 복잡 하며 여러 요인에 따라 다릅니다. 특히 Azure VM이 StorSimple 볼륨의 파일에 액세스할 수 있는 속도와 동기화 해야 하는 파일 및 폴더를 Azure File Sync 속도를 빠르게 처리할 수 있습니다. 

경험을 통해 대역폭 (따라서 실제 데이터 크기)이 하위 역할을 수행 한다고 가정할 수 있습니다. 이 또는 이후 마이그레이션 라운드를 사용 하는 시간은 주로 초당 처리할 수 있는 항목 수에 따라 달라 집니다. 따라서 예를 들어 10만 파일 및 폴더를 포함 하는 TiB 1은 5만 파일 및 폴더만 사용 하 여 1 TiB 보다 느리게 종료 될 수 있습니다.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>5 단계: 여러 볼륨 클론 반복

:::row:::
    :::column:::
        ![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        이전 단계에서 설명한 대로 초기 동기화는 시간이 오래 걸릴 수 있습니다. 사용자와 응용 프로그램이 여전히 온-프레미스 StorSimple 8100 또는 8600 어플라이언스에 액세스 하 고 있습니다. 즉, 변경 내용이 누적 되며 매일 라이브 데이터와 초기 볼륨 클론 사이에 더 큰 델타를 사용 하는 것은 현재 마이그레이션, 폼입니다. 이 섹션에서는 여러 볼륨 클론을 사용 하 고 동기화가 완료 되 면이를 알리는 방법으로 가동 중지 시간을 최소화 하는 방법을 알아봅니다.
    :::column-end:::
:::row-end:::

아쉽게도 마이그레이션 프로세스는 즉각적이 아닙니다. 즉, 사용 중인 데이터에 대해 언급 된 델타는 피할 수 없습니다. 새 볼륨 클론을 탑재 하는 과정을 반복할 수 있는 것이 좋습니다. 각 볼륨 클론의 델타는 점차 작아집니다. 따라서 궁극적으로 동기화는 사용자 및 앱이 온-프레미스 Windows server로 이동 하기 위해 오프 라인으로 전환 하는 데 적합 한 것으로 간주 되는 기간 내에 완료 됩니다.

사용자 및 앱을 오프 라인으로 전환 하는 데 충분 한 속도로 동기화가 완료 될 때까지 다음 단계를 반복 합니다.

1. [지정 된 볼륨 클론에 대 한 동기화가 완료 되었는지 확인 합니다.](#determine-when-sync-is-done)
2. [새 볼륨 클론을 가져와 8020 가상 어플라이언스에 탑재 합니다.](#the-next-volume-clones)
3. [동기화가 수행 되는 시기를 결정 합니다.](#determine-when-sync-is-done)
4. [자르기 전략](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>다음 볼륨 클론

이 문서 앞부분의 볼륨 클론을 만드는 방법에 대해 설명 했습니다.
이 단계에는 두 가지 작업이 있습니다.

1. [볼륨 클론 가져오기](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [해당 볼륨 클론 탑재 (위 참조)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>동기화가 수행 되는 시간 확인

동기화가 완료 되 면 시간 측정을 중지 하 고 볼륨 클론을 사용 하 고 탑재 하는 프로세스를 반복 해야 하는지 아니면 마지막 볼륨 클론에 대 한 동기화가 충분히 작은 지를 결정할 수 있습니다.

동기화가 완료 되었는지 확인 하려면 다음을 수행 합니다.

1. 이벤트 뷰어를 열고 **응용 프로그램 및 서비스** 로 이동 합니다.
2. **Microsoft\FileSync\Agent\Telemetry** 탐색 및 열기
3. 완료 된 동기화 세션에 해당 하는 최신 **이벤트 9102**을 찾습니다.
4. **세부 정보** 를 선택 하 고 **Syncdirection** 값이 **업로드** 인지 확인 합니다.
5. **HResult** 를 확인 하 고 **0**이 표시 되는지 확인 합니다. 즉, 동기화 세션이 성공 했음을 의미 합니다. HResult가 0이 아닌 값 이면 동기화 중에 오류가 발생 한 것입니다. **PerItemErrorCount** 가 0 보다 크면 일부 파일이 나 폴더가 제대로 동기화 되지 않은 것입니다. HResult는 0이지만 PerItemErrorCount가 0보다 클 수 있습니다. 이 시점에서 PerItemErrorCount에 대해 걱정할 필요가 없습니다. 이러한 파일은 나중에 살펴보겠습니다. 이 오류 수가 중요 하 고 수천 개의 항목이 면 고객 지원에 문의 하 고, 다음 단계에 대 한 직접 지침은 Azure File Sync 제품 그룹에 연결 해 달라고 요청 하십시오.
6. 행에서 HResult가 0 인 여러 9102 이벤트를 보려면 선택 합니다. 이는이 볼륨 클론에 대 한 동기화가 완료 되었음을 나타냅니다.

### <a name="cut-over-strategy"></a>자르기 전략

1. 볼륨 클론의 동기화가 지금 충분히 빠르게 진행 되는지 확인 합니다. (델타는 작아야 합니다.)
2. StorSimple 어플라이언스를 오프 라인으로 전환 합니다.
3. 최종 RoboCopy.

시간을 측정 하 고 최근 볼륨 클론의 동기화가 충분 한 시간 내에 완료 될 수 있는지 확인 합니다 .이는 시스템에서 가동 중지 시간을 감당할 수 있습니다.

이제 StorSimple 어플라이언스에 대 한 사용자 액세스를 사용 하지 않도록 설정 합니다. 변경 내용이 더 이상 없습니다. 가동 중지 시간이 시작 되었습니다.
어플라이언스를 온라인 상태로 유지 하 고 연결 해야 하지만 변경 내용을 방지 해야 합니다.

6 단계에서 마지막 볼륨 클론 이후 라이브 데이터에서 델타를 사용 하 여이를 처리 합니다.

## <a name="phase-6-a-final-robocopy"></a>6 단계: 최종 RoboCopy

이 시점에서 온-프레미스 Windows Server와 StorSimple 8100 또는 8600 어플라이언스 사이에는 두 가지 차이점이 있습니다.

1. 동기화 되지 않은 파일이 있을 수 있습니다 (위의 이벤트 로그에서 **Peritemerrors** 참조).
2. StorSimple 어플라이언스에는 채워진 캐시와 Windows Server가 현재 로컬에 저장 된 파일 콘텐츠가 없는 네임 스페이스만 있습니다.

![문서의이 하위 섹션에 집중 하는 데 도움이 되는 이전 개요 이미지의 일부를 보여 주는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Windows Server의 캐시를 어플라이언스의 상태로 전환 하 고 최종 RoboCopy를 사용 하 여 파일이 남아 있지 않은지 확인할 수 있습니다.

> [!CAUTION]
> 사용자가 수행 하는 RoboCopy 명령이 아래에 설명 된 것과 정확히 일치 해야 합니다. 이전에는 볼륨 클론 + 동기화 접근 방식을 통해 이동 하지 않은 로컬 파일 및 파일을 복사 하려고 합니다. 마이그레이션이 완료 된 후 나중에 동기화 되지 않은 문제를 해결할 수 있습니다. [Azure File Sync 문제 해결](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)을 참조 하세요. 파일이 삭제 될 때 누락 되지 않는 파일 이름에는 인쇄할 수 없는 문자가 있을 수 있습니다.

RoboCopy 명령:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

배경:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      는 RoboCopy가 다중 스레드를 실행할 수 있도록 합니다. 기본값은 8이 고 최대값은 128입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      로그 파일에 대 한 상태를 유니코드로 출력 합니다 (기존 로그 덮어쓰기).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /T
   :::column-end:::
   :::column span="1":::
      콘솔 창에 출력 합니다. 로그 파일에 대 한 출력과 함께 사용 됩니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      백업 응용 프로그램에서 사용 하는 것과 동일한 모드에서 RoboCopy를 실행 합니다. 이를 통해 RoboCopy는 현재 사용자에 게 권한이 없는 파일을 이동할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      RoboCopy가 원본 (StorSimple 어플라이언스)와 대상 (Windows Server 디렉터리) 간의 델타만 고려 하도록 허용 합니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      파일 복사의 충실도 (기본값은/COPY: DAT), 복사 플래그: D = 데이터, A = 특성, T = 타임 스탬프, S = 보안 = NTFS Acl, O = 소유자 정보, U = 감사 정보
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      모든 파일 정보 복사 (/COPY: DATSOU와 동일)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      디렉터리 복사본에 대 한 충실도 (기본값:/DCOPY: DA), 복사 플래그: D = 데이터, A = 특성, T = 타임 스탬프
   :::column-end:::
:::row-end:::

Azure 파일에 대 한 파일 동기화를 사용 하 여 구성 된 대상으로 Windows Server의 각 디렉터리에 대해이 RoboCopy 명령을 실행 해야 합니다.

이러한 명령 중 여러 개를 병렬로 실행할 수 있습니다.
이 RoboCopy 단계가 완료 되 면 이전에 StorSimple 어플라이언스와 같이 사용자 및 앱이 Windows 서버에 액세스 하도록 허용할 수 있습니다.

Robocopy 로그 파일을 참조 하 여 파일이 남아 있는지 확인 하십시오. 문제가 존재 해야 하는 경우 대부분의 경우 마이그레이션이 완료 되 고 사용자와 앱이 Windows 서버로 다시 홈 된 후에 해결할 수 있습니다. 문제를 해결 해야 하는 경우 7 단계 전에이 작업을 수행 합니다.

이전에 StorSimple 데이터에 대해 보유 한 Windows 서버에 SMB 공유를 만드는 것이 필요할 수 있습니다. 이 단계를 앞으로 로드 하 고 이전에 시간이 손실 되지 않도록 할 수 있지만,이 시점 이전에는 Windows server에서 파일 변경 내용이 발생 하지 않도록 해야 합니다.

DFS를 배포 하는 경우 DFN 네임 스페이스를 새 서버 폴더 위치로 가리킬 수 있습니다. 제어 되 배포를 사용 하지 않고 8100 8600 어플라이언스를 Windows Server를 사용 하 여 로컬로 연결 하는 경우 해당 서버를 도메인에서 분리 하 고 도메인에 대해 AFS를 사용 하 여 새 Windows Server를 도메인에 가입 하 고, 이전 서버와 동일한 서버 이름과 공유 이름을 지정 하 고, 새 서버로의 이동이 사용자에 게 투명 하 게 유지 됩니다. , 그룹 정책 또는 스크립트가 있습니다.

## <a name="phase-7-deprovision"></a>7 단계: 프로 비전 해제

마지막 단계 중에 여러 볼륨 클론을 반복 하 여 StorSimple 어플라이언스를 오프 라인으로 전환한 후 새 Windows Server에 대 한 사용자 액세스를 줄일 수 있었습니다.

이제 불필요 한 리소스의 프로 비전을 해제할 수 있습니다.
시작 하기 전에 프로덕션 환경에서 약간의 새로운 Azure File Sync 배포를 관찰 하는 것이 가장 좋습니다. 그러면 발생할 수 있는 문제를 해결 하는 옵션이 제공 됩니다.

최소한 며칠 이상에 대 한 AFS 배포를 충족 하 고 관찰 한 후에는 다음 순서로 리소스 프로 비전을 시작할 수 있습니다.

1. 파일 동기화를 통해 볼륨 클론의 데이터를 Azure 파일 공유로 이동 하는 데 사용한 Azure VM을 끕니다.
2. Azure에서 저장소 동기화 서비스 리소스로 이동 하 여 Azure VM의 등록을 취소 합니다. 모든 동기화 그룹에서 제거 합니다.

    > [!WARNING]
    > **올바른 컴퓨터를 선택 했는지 확인 합니다.** 클라우드 VM을 해제 했습니다. 즉, 등록 된 서버 목록에 유일한 오프 라인 서버로 표시 되어야 합니다. 이 단계에서는 온-프레미스 Windows Server를 선택 하지 않아야 합니다. 그렇게 하면 등록 취소 됩니다.

3. Azure VM 및 해당 리소스를 삭제 합니다.
4. 8020 가상 StorSimple 어플라이언스를 사용 하지 않도록 설정 합니다.
5. Azure에서 모든 StorSimple 리소스의 프로 비전을 해제 합니다.
6. 데이터 센터에서 StorSimple 물리적 어플라이언스를 분리 합니다.

마이그레이션이 완료 되었습니다.

## <a name="next-steps"></a>다음 단계

Azure File Sync에 좀 더 친숙 합니다. 특히 클라우드 계층화 정책의 유연성이 있습니다.

Azure Portal 또는 이전 이벤트에서 일부 파일이 영구적으로 동기화 되지 않는 것으로 표시 되는 경우 문제 해결 가이드를 검토 하 여 이러한 문제를 해결 하는 단계를 검토 합니다.

* [Azure File Sync 개요: aka.ms/AFS](https://aka.ms/AFS)
* [클라우드 계층화](storage-sync-cloud-tiering.md) 
* [Azure File Sync 문제 해결 가이드](storage-sync-files-troubleshoot.md)
