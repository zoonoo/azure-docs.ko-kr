---
title: StorSimple 8000 시리즈 Azure 파일 동기화로 마이그레이션
description: StorSimple 8100 또는 8600 어플라이언스를 Azure 파일 동기화로 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755037"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 및 8600 Azure 파일 동기화로 마이그레이션

StorSimple 8000 계열은 8100 또는 8600 물리적 온-프레미스 어플라이언스 및 클라우드 서비스 구성 요소로 표시됩니다. 이러한 어플라이언스 중 하나에서 Azure File Sync 환경으로 데이터를 마이그레이션할 수 있습니다. Azure 파일 동기화는 StorSimple 어플라이언스로 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 8000 시리즈는 2022년 12월에 [출시됩니다.](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 가능한 한 빨리 마이그레이션 계획을 시작하는 것이 중요합니다. 이 문서에서는 Azure File Sync로성공적으로 마이그레이션하는 데 필요한 배경 지식과 마이그레이션 단계를 제공합니다. 

## <a name="azure-file-sync"></a>Azure 파일 동기화

> [!IMPORTANT]
> Microsoft는 고객의 마이그레이션을 지원하기 위해 최선을 다하고 있습니다. .com으로 이메일을 AzureFilesMigration@microsoft 보내 마이그레이션 중 사용자 지정 마이그레이션 계획과 지원을 받으시면 됩니다.

Azure 파일 동기화는 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 파일 동기화 및 클라우드 계층화.
* 파일은 SMB 및 파일 REST와 같은 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 저장소로 공유됩니다. Azure 파일 공유는 기본적으로 네트워크 드라이브로 마운트할 수 있는 Windows Server의 파일 공유와 비슷합니다. 특성, 사용 권한 및 타임스탬프와 같은 중요한 파일 충실도 측면을 지원합니다. Azure 파일 공유를 사용하면 클라우드에 저장된 파일 및 폴더를 해석하기 위해 응용 프로그램이나 서비스가 더 이상 필요하지 않습니다. Windows 파일 탐색기와 같은 친숙한 프로토콜 및 클라이언트를 통해 기본적으로 액세스할 수 있습니다. 따라서 Azure 파일은 클라우드에 범용 파일 서버 데이터뿐만 아니라 일부 응용 프로그램 데이터를 저장하는 데 가장 이상적이고 유연한 접근 방식을 공유합니다.

이 문서에서는 마이그레이션 단계에 중점을 둡니다. 마이그레이션하기 전에 Azure File Sync에 대해 자세히 알아보려면 다음 문서를 참조하는 것이 좋습니다.

* [Azure 파일 동기화 - 개요](https://aka.ms/AFS "개요")
* [Azure 파일 동기화 - 배포 가이드](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>마이그레이션 목표

목표는 생산 데이터의 무결성을 보장하고 가용성을 보장하는 것입니다. 후자는 정기 유지 관리 기간에 적합하거나 약간만 초과할 수 있도록 가동 중지 시간을 최소한으로 유지해야 합니다.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Azure 파일 동기화로의 StorSimple 8000 시리즈 마이그레이션 경로

Azure 파일 동기화 에이전트를 실행하려면 로컬 Windows 서버가 필요합니다. Windows 서버는 최소 2012R2 서버일 수 있지만 이상적으로는 Windows 서버 2019입니다.

대체 마이그레이션 경로가 많으며 너무 오래 문서를 만들어 모든 마이그레이션을 문서화하고 이 문서에서 모범 사례로 권장하는 경로에 대해 위험이나 단점을 설명하는 방법을 설명합니다.

![StorSimple 8000 시리즈 마이그레이션 단계 개요](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 시리즈 마이그레이션 경로 개요는 이 문서에서 보다 아래에 있는 단계입니다.")

이전 이미지는 이 문서의 섹션에 해당하는 단계를 설명합니다.
로컬 StorSimple 어플라이언스에 대한 파일의 불필요한 리콜을 방지하기 위해 클라우드 측 마이그레이션을 사용합니다. 이 방법은 로컬 캐싱 동작 또는 네트워크 대역폭 사용에 영향을 주지 않도록 하며, 이 중 하나는 프로덕션 워크로드에 영향을 줄 수 있습니다.
클라우드 측 마이그레이션은 데이터의 스냅숏(볼륨 복제)에서 작동합니다. 따라서 프로덕션 데이터는 마이그레이션이 끝날 때까지 이 프로세스에서 격리됩니다. 기본적으로 백업을 작업하면 문제가 있을 경우 마이그레이션을 안전하고 쉽게 반복할 수 있습니다.

## <a name="considerations-around-existing-storsimple-backups"></a>기존 StorSimple 백업과 관련된 고려 사항

StorSimple볼륨 클론의 형태로 백업을 수행 할 수 있습니다. 이 문서에서는 새 볼륨 복제를 사용하여 라이브 파일을 마이그레이션합니다.
라이브 데이터 외에 백업을 마이그레이션해야 하는 경우 이 문서의 모든 지침이 계속 적용됩니다. 유일한 차이점은 새 볼륨 복제로 시작하는 대신 마이그레이션해야 하는 가장 오래된 백업 볼륨 복제본으로 시작한다는 것입니다.

순서는 다음과 같습니다.

* 마이그레이션해야 하는 최소 볼륨 클론 세트를 결정합니다. 마이그레이션하는 백업이 많을수록 전체 마이그레이션 프로세스가 오래 걸리므로 가능하면 이 목록을 최소한으로 유지하는 것이 좋습니다.
* 마이그레이션 프로세스를 진행할 때 마이그레이션하려는 가장 오래된 볼륨 복제본으로 시작하고 이후각 마이그레이션에서 다음 가장 오래된 복제를 사용합니다.
* 각 볼륨 복제 마이그레이션이 완료되면 Azure 파일 공유 스냅숏을 만들어야 합니다. [Azure 파일 공유 스냅숏은](storage-snapshots-files.md) Azure 파일 공유에 대한 파일 및 폴더 구조의 시점 간 백업을 유지하는 방법입니다. 마이그레이션이 완료된 후 마이그레이션을 진행할 때 각 볼륨 복제본의 버전을 보존해야 합니다.
* 동일한 StorSimple 볼륨에서 제공되는 모든 Azure 파일 공유 스냅숏을 생성해야 합니다. 볼륨 클론은 볼륨 수준에 있고 Azure 파일 공유 스냅숏은 공유 수준에 있습니다. 볼륨 복제본마이그레이션이 완료된 후 각 Azure 파일 공유에서 공유 스냅숏을 만들어야 합니다.
* 볼륨 복제에 대한 마이그레이션 프로세스를 반복하고 각 볼륨 복제 후 공유 스냅숏을 라이브 데이터의 스냅숏에 따라 잡을 때까지 스냅숏을 생성합니다. 볼륨 복제본을 마이그레이션하는 프로세스는 아래 단계에 설명되어 있습니다. 

백업을 전혀 이동할 필요가 없고 라이브 데이터만 마이그레이션한 후 Azure 파일 공유 측에서 새 백업 체인을 시작할 수 있는 경우 마이그레이션의 복잡성과 마이그레이션에 걸리는 시간을 줄이는 것이 좋습니다. StorSimple에 있는 각 볼륨(각 공유가 아님)에 대해 백업을 이동할지 여부와 각 볼륨에 대한 수를 결정할 수 있습니다.

## <a name="phase-1-get-ready"></a>1단계: 준비

:::row:::
    :::column:::
        ![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        마이그레이션의 기초는 볼륨 클론과 StorSimple 8020이라는 가상 클라우드 어플라이언스입니다.
이 단계에서는 Azure에서 이러한 리소스의 배포에 중점을 둡니다.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>StorSimple 8020 가상 어플라이언스 배포

클라우드 어플라이언스를 배포하는 것은 보안, 네트워킹 및 몇 가지 다른 고려 사항이 필요한 프로세스입니다.

> [!IMPORTANT]
> 다음 가이드에는 몇 가지 불필요한 섹션이 포함되어 있습니다. 처음부터 "3단계"까지 기사를 읽고 따르십시오. 그런 다음 이 문서로 돌아갑니다. 이 시점에서 해당 가이드에서 "3단계" 또는 그 이상의 것을 완료할 필요가 없습니다.

[StorSimple 8020 가상 어플라이언스 배포](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>사용할 볼륨 복제본 결정

마이그레이션을 시작할 준비가 되면 첫 번째 단계는 백업과 마찬가지로 StorSimple 클라우드 저장소의 현재 상태를 캡처하는 새 볼륨 복제를 취하는 것입니다. 당신이 가지고있는 Stor간단한 볼륨의 각각에 대한 복제를 가져 가라.
백업을 이동해야 하는 경우 사용하는 첫 번째 볼륨 복제본은 새로 만든 복제본이 아니라 마이그레이션해야 하는 가장 오래된 볼륨 복제본(가장 오래된 백업)입니다.
자세한 지침은 ["기존 StorSimple 백업에 대한 고려 사항"](#considerations-around-existing-storsimple-backups) 섹션을 참조하십시오.

> [!IMPORTANT]
> 다음 가이드에는 몇 가지 불필요한 섹션이 포함되어 있습니다. 링크된 섹션에 설명된 단계만 읽고 따릅니다. 그런 다음 이 문서로 돌아갑니다. "다음 단계" 섹션을 따를 필요는 없습니다.

[볼륨의 클론 만들기](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>볼륨 복제 사용

1단계의 마지막 단계는 선택한 볼륨 복제를 Azure의 8020 가상 어플라이언스에서 사용할 수 있도록 하는 것입니다.

> [!IMPORTANT]
> 다음 가이드에는 필요한 단계뿐만 아니라 끝에 있는 볼륨을 포맷하는 명령이 포함되어 있습니다. **볼륨의 포맷을 지정하지 마십시오.** 처음부터 "섹션 7"을 읽고 따르십시오: "10. 간단한 볼륨을 포맷하려면 ..."  이 단계를 따르기 전에 중지하고 이 문서로 돌아갑니다.

[Azure의 8020 가상 어플라이언스에 볼륨 복제본 마운트](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>1단계 요약

이제 1단계를 완료했습니다.

* Azure에 StorSimple 8020 가상 어플라이언스를 배포했습니다.
* 마이그레이션을 시작할 볼륨 복제를 결정했습니다.
* 볼륨 클론(각 라이브 볼륨에 대해 하나씩)을 Azure의 StorSimple 가상 어플라이언스에 탑재하고 데이터를 추가로 사용할 수 있습니다.

## <a name="phase-2-cloud-vm"></a>2단계: 클라우드 VM

:::row:::
    :::column:::
        ![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Azure의 StorSimple 8020 가상 어플라이언스에서 초기 복제를 사용할 수 있게 된 후 이제 VM을 프로비전하고 볼륨 클론(또는 여러 개)을 iSCSI를 통해 해당 VM에 노출할 차례입니다.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Azure VM 배포

Azure의 Windows Server 가상 컴퓨터는 마이그레이션 중에만 필요한 임시 인프라인 StorSimple 8020과 같습니다.
배포하는 VM의 구성은 주로 동기화할 항목(파일 및 폴더)의 수에 따라 달라집니다. 문제가 있는 경우 더 높은 성능 구성을 사용할 것을 권장합니다.

단일 Windows 서버는 최대 30개의 Azure 파일 공유를 동기화할 수 있습니다.
모든 공유/경로 또는 StorSimple 볼륨의 루트를 포괄하고 항목(파일 및 폴더)을 계산해야 한다고 결정하는 사양입니다.

데이터의 전체 크기는 병목 현상이 적습니다 .

* [동기화해야 하는 항목(파일 및 폴더) 수에 따라 Windows 서버의 크기를 조정하는 방법을 알아봅니다.](storage-sync-files-planning.md#recommended-system-resources)

    **참고 사항:** 이전에 연결된 아티클은 RAM(서버 메모리)에 대한 범위가 있는 테이블을 제공합니다. Azure VM의 큰 숫자를 향합니다. 온-프레미스 머신의 작은 숫자를 향할 수 있습니다.

* [Windows 서버 VM을 배포하는 방법에 대해 알아봅니다.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> VM이 StorSimple 8020 가상 어플라이언스와 동일한 Azure 지역에 배포되어 있는지 확인합니다. 이 마이그레이션의 일부로 현재 저장된 리전에서 클라우드 데이터의 영역을 변경해야 하는 경우 Azure 파일 공유를 프로비전할 때 이후 단계에서 이를 수행할 수 있습니다.

> [!IMPORTANT]
> 온-프레미스 Windows 서버는 온-프레미스 StorSimple 어플라이언스를 앞에 두는 데 사용되는 경우가 많습니다. 이러한 구성에서는 해당 Windows 서버에서["데이터 중복 제거"](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)기능을 활성화할 수 있습니다. **StorSimple 데이터와 함께 데이터 중복 제거를 사용하는 경우 이 Azure VM에서도 데이터 중복 제거를 사용하도록 설정해야 합니다.** 이 파일 수준 중복 제거를 StorSimples 기본 제공 블록 수준 중복 제거와 혼동하지 마십시오.

> [!IMPORTANT]
> 성능을 최적화하려면 클라우드 VM에 **빠른 OS 디스크를** 배포합니다. 모든 데이터 볼륨에 대한 동기화 데이터베이스를 OS 디스크에 저장합니다. 또한 **큰 OS 디스크를**만들어야 합니다. StorSimple 볼륨의 항목 수(파일 및 폴더)에 따라 OS 디스크에는 동기화 데이터베이스를 수용하기 위해 **수백 GiB의** 공간이 필요할 수 있습니다.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>StorSimple 8020 볼륨을 Azure VM에 노출

이 단계에서는 iSCSI를 통해 8020 가상 어플라이언스에서 프로비전한 Windows 서버 VM에 하나 또는 여러 개의 StorSimple 볼륨을 연결합니다.

> [!IMPORTANT]
> 다음 문서의 경우 **클라우드 어플라이언스에 대한 개인 IP 받기만** 완료하고 **iSCSI** 섹션을 통해 연결하고 이 문서로 돌아갑니다.

1. [클라우드 어플라이언스에 대한 프라이빗 IP 가져오기](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [iSCSI를 통해 연결](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>2단계 요약

이제 2단계를 완료했습니다. 

* 8020 가상 StorSimple 어플라이언스와 동일한 리전에서 Windows 서버 VM 프로비전
* iSCSI를 통해 8020에서 Windows 서버 VM에 모든 해당 볼륨을 노출했습니다.
* 이제 마운트된 볼륨에서 서버 VM에서 파일 탐색기를 사용할 때 파일 및 폴더 콘텐츠가 표시됩니다.

마이그레이션이 필요한 모든 볼륨에 대해 이러한 단계를 완료한 경우에만 3단계로 진행합니다.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>3단계: Azure 파일 공유 를 설정하고 Azure 파일 동기화준비

:::row:::
    :::column:::
        ![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        이 단계에서는 여러 Azure 파일 공유를 결정하고 프로비전하고, Windows Server온-프레미스를 StorSimple 어플라이언스 대체로 만들고, Azure File Sync에 대해 해당 서버를 구성합니다. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>기존 네임스페이스를 Azure 파일 공유에 매핑

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure 파일 공유 배포

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> StorSimple 데이터가 있는 현재 리전에서 Azure 영역을 변경해야 하는 경우 사용하려는 새 리전에서 Azure 파일 공유를 프로비전합니다. Azure 파일 공유를 보유하는 저장소 계정을 프로비전할 때 지역을 선택하여 지역을 결정합니다. 아래에 프로비전할 Azure File Sync 리소스도 동일한 새 리전에 있는지 확인합니다.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> StorSimple 데이터가 있는 현재 리전에서 Azure 지역을 변경해야 하는 경우 새 리전에서 Azure 파일 공유에 대한 저장소 계정을 프로비전한 것입니다. 이 저장소 동기화 서비스를 배포할 때 동일한 지역을 선택했는지 확인합니다.

### <a name="deploy-an-on-premises-windows-server"></a>온-프레미스 Windows 서버 배포

* Windows 서버 2019(최소 2012R2)를 가상 컴퓨터 또는 물리적 서버로 만듭니다. Windows 서버 장애 조치 클러스터도 지원됩니다. StorSimple 8100 또는 8600을 정면으로 사용할 수 있는 서버를 다시 사용하지 마십시오.
* 직접 연결된 저장소를 프로비전하거나 추가합니다(지원되지 않는 NAS와 비교하여 DAS).

새 Windows Server에 StorSimple 8100 또는 8600 어플라이언스가 로컬로 캐싱할 수 있는 것보다 동일하거나 더 많은 양의 저장소를 제공하는 것이 좋습니다. StorSimple 어플라이언스를 사용한 것과 동일한 방식으로 Windows Server를 사용하며 어플라이언스와 동일한 양의 저장소가 있는 경우 캐싱 환경이 동일하지 않은 경우 와 유사해야 합니다.
Windows 서버에서 원하는 수의 저장소를 추가하거나 제거할 수 있습니다. 이렇게 하면 로컬 볼륨 크기와 캐싱에 사용할 수 있는 로컬 저장소의 양을 확장할 수 있습니다.

### <a name="prepare-the-windows-server-for-file-sync"></a>파일 동기화를 위해 Windows 서버 준비

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Windows 서버에서 Azure 파일 동기화 구성

등록된 온-프레미스 Windows Server는 이 프로세스를 위해 인터넷에 연결될 준비가 되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **클라우드 계층화를 켜야 합니다!** 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 저장 용량을 가질 수 있지만 전체 네임스페이스를 사용할 수 있도록 하는 AFS 기능입니다. 로컬에서 흥미로운 데이터도 로컬로 캐시되어 빠른 로컬 액세스 성능을 제공합니다. 이 단계에서 클라우드 계층을 설정하는 또 다른 이유는 이 단계에서 파일 콘텐츠를 동기화하지 않으려는 경우 이 때 네임스페이스만 이동해야 하기 때문입니다.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>4단계: 동기화를 위해 Azure VM 구성

:::row:::
    :::column:::
        ![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        이 단계에서는 iSCSI가 탑재된 첫 번째 볼륨 클론을 통해 Azure VM과 관련이 있습니다. 이 단계에서는 Azure 파일 동기화를 통해 VM을 연결하고 StorSimple 볼륨 복제에서 파일을 이동하는 첫 번째 라운드를 시작합니다.
        
    :::column-end:::
:::row-end:::

Azure File Sync에 대해 StorSimple 8100 또는 8600 어플라이언스를 대체할 온-프레미스 서버를 이미 구성했습니다. 

Azure VM 구성은 한 단계의 추가 단계로 거의 동일한 프로세스입니다. 다음 단계는 프로세스를 안내합니다.

> [!IMPORTANT]
> Azure VM이 **클라우드 계층화 사용하도록 구성되지 않은** 것이 중요합니다. 마이그레이션 전반에 걸쳐 이 서버의 볼륨을 새 볼륨 클론으로 교환합니다. 클라우드 계층화는 CPU 사용량에 대한 이점과 오버헤드가 없습니다.

1. [AFS 에이전트를 배포합니다. (이전 섹션 참조)](#prepare-the-windows-server-for-file-sync)
2. [Azure 파일 동기화에 대한 VM 준비.](#get-the-vm-ready-for-azure-file-sync)
3. [동기화 구성](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Azure 파일 동기화에 대한 VM 준비

Azure 파일 동기화는 마운트된 iSCSI StorSimple 볼륨에서 대상 Azure 파일 공유로 파일을 이동하는 데 사용됩니다.
이 마이그레이션 프로세스 동안 동일한 드라이브 문자 아래에 여러 볼륨 클론을 VM에 탑재합니다. Azure 파일 동기화는 파일 및 폴더의 최신 버전으로 탑재한 다음 볼륨 복제를 보고 Azure File Sync를 통해 연결된 Azure 파일 공유를 업데이트하도록 구성해야 합니다. 

> [!IMPORTANT]
> 이 작업을 수행하려면 Azure File Sync를 구성하기 전에 레지스트리 키를 서버에 설정해야 합니다.

1. VM의 시스템 드라이브에 새 디렉터리를 만듭니다. Azure 파일 동기화 정보는 탑재된 볼륨 클론 대신 에 있어야 합니다. 예: `"C:\syncmetadata"`
2. regedit를 열고 다음 레지스트리 하이브를 찾습니다.`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. ***이름: MetadataRootPath형식*** 문자열의 새 키 만들기
4. 예를 들어 시스템 볼륨에서 만든 디렉터리로 의 전체 경로를 설정합니다.`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure VM에서 Azure 파일 동기화 구성

이 단계는 온-프레미스 서버에서 AFS를 구성하는 방법에 대해 설명하는 이전 섹션과 유사합니다.

차이점은 이 서버에서 클라우드 계층을 사용하도록 설정하면 안 되며 올바른 폴더가 올바른 Azure 파일 공유에 연결되어 있는지 확인해야 한다는 것입니다. 그렇지 않으면 Azure 파일 공유 및 데이터 콘텐츠의 이름 지정이 일치하지 않으며 동기화를 다시 구성하지 않고 클라우드 리소스 또는 로컬 폴더의 이름을 바꿀 수 없습니다.

[Windows 서버에서 Azure 파일 동기화를 구성하는 방법에 대한 이전 섹션을](#configure-azure-file-sync-on-the-windows-server)참조하십시오.

### <a name="step-4-summary"></a>4단계 요약

이 시점에서 iSCSI를 통해 StorSimple 볼륨 복제본을 탑재한 Azure VM에서 Azure 파일 동기화를 성공적으로 구성했습니다.
이제 데이터가 Azure VM에서 다양한 Azure 파일 공유로 흐르고 있으며, 거기에서 온-프레미스 Windows Server에 완전히 피곤한 네임스페이스가 나타납니다.

> [!IMPORTANT]
> 현재 Windows Server에 대한 변경 내용이나 사용자 액세스권한이 부여되지 않았는지 확인합니다.

Azure VM을 통해 Azure 파일 공유로 이동하는 초기 볼륨 복제 데이터는 몇 주, 잠재적으로 시간이 오래 걸릴 수 있습니다. 이 시간이 걸리는 시간을 예측하는 것은 까다롭고 많은 요인에 따라 달라집니다. 특히 Azure VM이 StorSimple 볼륨의 파일에 액세스할 수 있는 속도와 Azure File Sync가 동기화가 필요한 파일 및 폴더를 처리하는 속도가 가장 빠릅니다. 

경험에 따르면 대역폭(따라서 실제 데이터 크기)이 하위 역할을 한다고 가정할 수 있습니다. 이 또는 후속 마이그레이션 라운드가 걸리는 시간은 대부분 초당 처리할 수 있는 항목 수에 따라 달라집니다. 예를 들어 100,000개의 파일과 폴더가 있는 TiB 1은 50,000개의 파일과 폴더만 있는 1TiB보다 느리게 완료될 가능성이 높습니다.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>5단계: 여러 볼륨 클론을 통해 반복

:::row:::
    :::column:::
        ![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        이전 단계에서 설명한 대로 초기 동기화에는 시간이 오래 걸릴 수 있습니다. 사용자와 응용 프로그램은 여전히 온-프레미스 StorSimple 8100 또는 8600 어플라이언스에 액세스하고 있습니다. 즉, 변경 내용이 누적되고 있으며 라이브 데이터와 초기 볼륨 복제 간에 매일 더 큰 델타가 형성되면서 현재 마이그레이션되는 양식이 됩니다. 이 섹션에서는 여러 볼륨 클론을 사용하고 동기화가 완료된 시기를 알려 가동 중지 시간을 최소화하는 방법을 알아봅니다.
    :::column-end:::
:::row-end:::

안타깝게도 마이그레이션 프로세스는 즉각적이지 않습니다. 즉, 라이브 데이터에 앞서 언급한 델타는 피할 수 없는 결과입니다. 좋은 소식은 새 볼륨 클론을 장착하는 과정을 반복할 수 있다는 것입니다. 각 볼륨 클론의 델타는 점진적으로 작아집니다. 따라서 결국 동기화는 사용자와 앱을 오프라인으로 이동하여 온-프레미스 Windows 서버로 전환하는 것이 허용되는 시간 내에 완료됩니다.

동기화가 사용자와 앱을 오프라인으로 전환하는 데 편안함을 느낄 만큼 빠른 속도로 동기화가 완료될 때까지 다음 단계를 반복합니다.

1. [지정된 볼륨 복제에 대해 동기화가 완료되었는지 확인합니다.](#determine-when-sync-is-done)
2. [새 볼륨 클론을 사용하여 8020 가상 어플라이언스에 마운트합니다.](#the-next-volume-clones)
3. [동기화가 완료되는 시기를 결정합니다.](#determine-when-sync-is-done)
4. [컷오버 전략](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>다음 볼륨 복제본

이 문서의 앞에서 볼륨 복제를 복용하는 것에 대해 설명했습니다.
이 단계에는 다음 두 가지 작업이 있습니다.

1. [볼륨 복제하기](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [해당 볼륨 복제본 마운트(위 참조)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>동기화가 완료된 시기 결정

동기화가 완료되면 시간 측정을 중지하고 볼륨 복제를 수행하고 장착하는 과정을 반복해야 하는지 또는 마지막 볼륨 복제본과의 시간 동기화가 충분히 작은지 확인할 수 있습니다.

동기화가 완료되었는지 확인하려면 다음을 수행하십시오.

1. 이벤트 뷰어를 열고 **응용 프로그램 및 서비스로** 이동
2. 탐색 및 열기 **마이크로 소프트\FileSync\에이전트\원격 분석**
3. 완료된 동기화 세션에 해당하는 최신 **이벤트 9102를**찾습니다.
4. **세부 정보를** 선택하고 **SyncDirection** 값이 **업로드되어** 있는지 확인합니다.
5. **HResult를** 확인하고 **0을**표시하고 있는지 확인합니다. 즉, 동기화 세션이 성공했습니다. HResult가 0이 아닌 값인 경우 동기화 중에 오류가 발생했습니다. **PerItemErrorCount가** 0보다 크면 일부 파일이나 폴더가 제대로 동기화되지 않은 것입니다. HResult는 0이지만 PerItemErrorCount가 0보다 클 수 있습니다. 이 시점에서 PerItemErrorCount에 대해 걱정할 필요가 없습니다. 우리는 나중에이 파일을 잡을 것입니다. 이 오류 수가 중요한 경우 수천 개의 항목이 고객 지원에 문의하고 Azure File Sync 제품 그룹에 연결하여 다음 단계에 대한 직접적인 지침을 요청합니다.
6. HResult 0이 행에 있는 여러 9102 이벤트를 보려면 확인합니다. 이 볼륨 복제에 대한 동기화가 완료된 것을 나타냅니다.

### <a name="cut-over-strategy"></a>컷오버 전략

1. 볼륨 복제본의 동기화가 이제 충분히 빠른지 확인합니다. (델타 충분히 작은.)
2. StorSimple 어플라이언스를 오프라인으로 사용하십시오.
3. 마지막 로보카피.

시간을 측정하고 최근 볼륨 복제본의 동기화가 시간 창 에서 충분히 작은 시간 내에 완료될 수 있는지 확인하여 시스템에서 가동 중지 시간을 감당할 수 있습니다.

이제 StorSimple 어플라이언스에 대한 사용자 액세스를 비활성화할 때입니다. 더 이상 변경되지 않습니다. 가동 중지 시간이 시작되었습니다.
어플라이언스를 온라인 상태로 두고 연결해야 하지만 이제 어플라이언스의 변경을 방지해야 합니다.

6단계에서는 마지막 볼륨 복제 이후 라이브 데이터의 델타를 따라잡을 수 있습니다.

## <a name="phase-6-a-final-robocopy"></a>6단계: 최종 로보카피

이 시점에서 온-프레미스 Windows 서버와 StorSimple 8100 또는 8600 어플라이언스 간에는 두 가지 차이점이 있습니다.

1. 동기화되지 않은 파일이 있을 수 있습니다(위의 이벤트 로그에서 **PerItemErrors** 참조).
2. StorSimple 어플라이언스는 현재 로컬에 저장된 파일 콘텐츠가 없는 네임스페이스와 Windows Server의 채우기 캐시를 가지고 있습니다.

![문서의 이 하위 섹션에 초점을 맞추는 데 도움이 되는 이전 개요 이미지의 일부를 설명하는 이미지입니다.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Windows Server의 캐시를 어플라이언스 상태로 가져오고 최종 RoboCopy로 파일이 남아 있지 않도록 할 수 있습니다.

> [!CAUTION]
> 당신이 따르는 RoboCopy 명령은 아래에 설명된 것과 정확히 동일해야 합니다. 이전에 볼륨 복제 +sync 접근 방식을 통해 이동하지 않은 로컬 파일과 파일만 복사하려고 합니다. 마이그레이션이 완료된 후 나중에 동기화되지 않은 문제를 해결할 수 있습니다. (Azure [파일 동기화 문제 해결](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)을 참조하십시오. 파일 이름에 인쇄할 수 없는 문자가 삭제될 때 놓치지 않을 가능성이 큽니다.)

로보카피 명령:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

배경:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy가 다중 스레드를 실행할 수 있습니다. 기본값은 8, 최대값은 128입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /유니로그:<file name>
   :::column-end:::
   :::column span="1":::
      LOG 파일에 상태를 UNICODE로 출력합니다(기존 로그를 덮어씁니다).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      콘솔 창으로 출력합니다. 로그 파일에 대한 출력과 함께 사용됩니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      백업 응용 프로그램에서 사용하는 것과 동일한 모드에서 RoboCopy를 실행합니다. RoboCopy를 사용하면 현재 사용자에게 사용 권한이 없는 파일을 이동할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /mir
   :::column-end:::
   :::column span="1":::
      RoboCopy에서 소스(StorSimple 어플라이언스)와 대상(Windows Server 디렉터리) 간의 델타만 고려할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:카피플래그[들]
   :::column-end:::
   :::column span="1":::
      파일 복사본의 충실도(기본값은 /COPY:DAT), 복사 플래그: D=데이터, A=특성, T=타임스탬프, S=보안=NTFS ACL, O=소유자 정보, U=AUditing 정보
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      모든 파일 정보 복사(/COPY:DATSOU)에 해당)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:카피플래그[들]
   :::column-end:::
   :::column span="1":::
      디렉터리 복사본에 대한 충실도(기본값은 /DCOPY:DA), 복사 플래그: D=데이터, A=특성, T=타임스탬프
   :::column-end:::
:::row-end:::

Azure 파일에 대한 파일 동기화로 구성한 Windows Server의 각 디렉터리에 대해 이 RoboCopy 명령을 대상으로 실행해야 합니다.

이러한 명령 중 여러 명령을 병렬로 실행할 수 있습니다.
이 RoboCopy 단계가 완료되면 사용자와 앱이 이전에 StorSimple 어플라이언스를 했던 것처럼 Windows 서버에 액세스하도록 허용할 수 있습니다.

로보카피 로그 파일을 참조하여 파일이 남아 있는지 확인합니다. 문제가 있는 경우 대부분의 경우 마이그레이션이 완료되고 사용자와 앱이 Windows Server에 다시 연결된 후에 해결할 수 있습니다. 문제를 해결해야 하는 경우 7단계 전에 해결하십시오.

이전에 StorSimple 데이터에 있던 Windows 서버에서 SMB 공유를 만들어야 할 수 있습니다. 이 단계를 미리 로드하고 여기에서 시간을 잃지 않도록 일찍 수행할 수 있지만 이 시점 이전에는 Windows 서버에서 파일이 변경되지 않도록 해야 합니다.

DFS-N 배포가 있는 경우 DFN-네임스페이스를 새 서버 폴더 위치로 가리킬 수 있습니다. DFS-N 배포가 없고 Windows Server를 사용하여 로컬로 8100 8600 어플라이언스를 접한 경우 해당 서버를 도메인에서 해제하고 도메인에 AFS를 사용하여 새 Windows Server에 가입하고 이전 서버와 동일한 서버 이름을 지정하고 동일한 공유 이름을 지정한 다음 새 서버에 대한 잘라내기가 사용자에게 투명하게 유지됩니다. 을 참조하십시오.

## <a name="phase-7-deprovision"></a>7단계: 프로비저닝 해제

마지막 단계에서는 여러 볼륨 클론을 반복했고 결국 StorSimple 어플라이언스를 오프라인으로 가져간 후 새 Windows Server에 대한 사용자 액세스를 줄일 수 있었습니다.

이제 불필요한 리소스의 프로비저닝을 해제할 수 있습니다.
시작하기 전에 프로덕션 환경에서 새 Azure File Sync 배포를 잠시 관찰하는 것이 좋습니다. 이렇게 하면 발생할 수 있는 문제를 해결할 수 있는 옵션이 제공됩니다.

만족하고 적어도 며칠 동안 AFS 배포를 관찰한 후에는 다음 순서로 리소스 프로비저닝을 해제할 수 있습니다.

1. 볼륨 클론에서 파일 동기화를 통해 Azure 파일 공유로 데이터를 이동하는 데 사용한 Azure VM을 끕니다.
2. Azure의 저장소 동기화 서비스 리소스로 이동하여 Azure VM을 등록 취소합니다. 즉, 모든 동기화 그룹에서 제거합니다.

    > [!WARNING]
    > **올바른 기계를 선택하십시오.** 클라우드 VM을 끄면 등록된 서버 목록에 있는 유일한 오프라인 서버로 표시되어야 합니다. 이 단계에서는 온-프레미스 Windows Server를 선택하지 않아야 하며 이렇게 하면 등록이 취소됩니다.

3. Azure VM 및 해당 리소스를 삭제합니다.
4. 8020 가상 StorSimple 어플라이언스를 사용하지 않도록 설정합니다.
5. Azure의 모든 StorSimple 리소스프로비전 해제합니다.
6. 데이터 센터에서 StorSimple 물리적 어플라이언스의 플러그를 뽑습니다.

마이그레이션이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

Azure 파일 동기화에 대해 더 잘 알고 있습니다. 특히 클라우드 계층화 정책의 유연성이 있습니다.

Azure 포털 또는 이전 이벤트에서 일부 파일이 영구적으로 동기화되지 않는 경우 문제 해결 가이드를 검토하여 이러한 문제를 해결하는 단계를 확인합니다.

* [Azure 파일 동기화 개요: aka.ms/AFS](https://aka.ms/AFS)
* [클라우드 계층화](storage-sync-cloud-tiering.md) 
* [Azure 파일 동기화 문제 해결 가이드](storage-sync-files-troubleshoot.md)
