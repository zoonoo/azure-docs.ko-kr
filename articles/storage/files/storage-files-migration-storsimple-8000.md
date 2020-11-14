---
title: Azure File Sync로 StorSimple 8000 시리즈 마이그레이션
description: Azure File Sync로 StorSimple 8100 또는 8600 어플라이언스를 마이그레이션하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 046cca4e683a8f14893bf48ac8601b138a7c28a7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630280"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Azure File Sync로 StorSimple 8100 및 8600 마이그레이션

StorSimple 8000 시리즈는 8100 또는 8600 물리적, 온-프레미스 어플라이언스 및 클라우드 서비스 구성 요소로 표시 됩니다. 이러한 어플라이언스의 데이터를 Azure File Sync 환경으로 마이그레이션할 수 있습니다. Azure File Sync는 StorSimple 어플라이언스를 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 8000 시리즈는 12 월 2022에 [수명 끝](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) 에 도달 하 게 됩니다. 가능한 한 빨리 마이그레이션 계획을 시작 하는 것이 중요 합니다. 이 문서에서는 Azure File Sync로 성공적으로 마이그레이션하기 위한 필요한 배경 정보 및 마이그레이션 단계를 제공 합니다.

## <a name="phase-1-prepare-for-migration"></a>1 단계: 마이그레이션 준비

이 섹션에는 StorSimple 볼륨에서 Azure 파일 공유로의 마이그레이션이 시작 될 때 수행 해야 하는 단계가 포함 되어 있습니다.

### <a name="inventory"></a>재고

마이그레이션 계획을 시작할 때 먼저 마이그레이션에 필요한 모든 StorSimple 어플라이언스 및 볼륨을 식별 합니다. 이 작업을 완료 한 후에는 최적의 마이그레이션 경로를 결정할 수 있습니다.

* StorSimple 물리적 어플라이언스 (8000 시리즈)는이 마이그레이션 가이드를 사용 합니다.
* 가상 어플라이언스, [StorSimple 1200 시리즈는 다른 마이그레이션 가이드를 사용](storage-files-migration-storsimple-1200.md)합니다.

### <a name="migration-cost-summary"></a>마이그레이션 비용 요약

StorSimple Data Manager 리소스의 데이터 변환 서비스 작업을 통해 StorSimple 볼륨에서 Azure 파일 공유로의 마이그레이션은 무료로 제공 됩니다. 다른 비용은 마이그레이션 도중 및 마이그레이션 후에 발생할 수 있습니다.

* **네트워크 송신:** StorSimple 파일은 특정 Azure 지역 내의 저장소 계정에 있습니다. Azure 파일 공유를 프로 비전 하는 경우 동일한 Azure 지역에 있는 저장소 계정으로 마이그레이션하는 경우 송신 비용이 발생 하지 않습니다. 이 마이그레이션의 일부로 다른 지역의 저장소 계정으로 파일을 이동할 수 있습니다. 이 경우에는 송신 비용이 적용 됩니다.
* **Azure 파일 공유 트랜잭션:** 파일이 Azure 파일 공유에 복사 될 때 (마이그레이션의 일부로 또는 외부에서) 파일 및 메타 데이터가 작성 될 때 트랜잭션 비용이 적용 됩니다. 마이그레이션 중에 트랜잭션 최적화 계층에서 Azure 파일 공유를 시작 하는 것이 가장 좋습니다. 마이그레이션이 완료 된 후 원하는 계층으로 전환 합니다. 다음 단계는 적절 한 지점에서이를 호출 합니다.
* **Azure 파일 공유 계층 변경:** Azure 파일 공유의 계층을 변경 하면 트랜잭션 비용이 청구 됩니다. 대부분의 경우에는 이전 점에서 조언을 따르는 것이 더 효율적입니다.
* **저장소 비용:** 이 마이그레이션이 Azure 파일 공유에 파일을 복사 하기 시작 하면 Azure Files 저장소가 사용 되 고 청구 됩니다.
* **StorSimple:** StorSimple 장치 및 저장소 계정 프로 비전을 해제할 기회가 있을 때까지 저장소, 백업 및 어플라이언스에 대 한 StorSimple 비용은 계속 발생 합니다.

### <a name="direct-share-access-vs-azure-file-sync"></a>직접 공유-액세스 및 Azure File Sync

Azure 파일 공유는 파일 서비스 배포를 구성 하기 위한 새로운 세계 세계 전체를 엽니다. Azure 파일 공유는 기본적으로 작동 하는 친숙 한 Kerberos 인증 및 기존 NTFS 권한 (파일 및 폴더 Acl)을 사용 하 여 사용자가 SMB 프로토콜을 통해 직접 액세스 하도록 설정할 수 있는 클라우드의 SMB 공유 일 뿐입니다. [Azure 파일 공유에 대 한 id 기반 액세스](storage-files-active-directory-overview.md)에 대해 자세히 알아보세요.

직접 액세스에 대 한 대안은 [Azure File Sync](./storage-sync-files-planning.md)입니다. Azure File Sync은 온-프레미스에서 자주 사용 하는 파일을 캐시 하는 StorSimple의 직접적인 아날로그 기능입니다.

Azure File Sync는 다음과 같은 두 가지 주요 구성 요소를 기반으로 하는 Microsoft 클라우드 서비스입니다.

* 파일 동기화 및 클라우드 계층화.
* 파일 공유는 SMB 및 파일 REST와 같은 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 저장소로 공유 됩니다.

Azure 파일 공유는 특성, 권한, 타임 스탬프 등의 저장 된 파일에 대 한 중요 한 파일 충실도 측면을 유지 합니다. Azure 파일 공유를 사용 하면 응용 프로그램 또는 서비스에서 클라우드에 저장 된 파일 및 폴더를 해석할 필요가 없습니다. Windows 파일 탐색기와 같은 친숙 한 프로토콜 및 클라이언트를 통해 기본적으로 액세스할 수 있습니다. Azure 파일 공유를 사용 하면 범용 파일 서버 데이터 및 응용 프로그램 데이터를 클라우드에 저장할 수 있습니다. Azure 파일 공유의 백업은 기본 제공 되는 기능이 며 Azure Backup 하 여 더욱 향상 시킬 수 있습니다.

이 문서에서는 마이그레이션 단계를 중점적으로 설명 합니다. 마이그레이션하기 전에 Azure File Sync에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure File Sync 개요](./storage-sync-files-planning.md "개요")
* [Azure File Sync 배포 가이드](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple 서비스 데이터 암호화 키

StorSimple 어플라이언스를 처음 설정 하는 경우 서비스 데이터 암호화 키를 생성 하 고 키를 안전 하 게 저장 하도록 지시 합니다. 이 키는 StorSimple 어플라이언스에서 파일을 저장 하는 연결 된 Azure storage 계정의 모든 데이터를 암호화 하는 데 사용 됩니다.

성공적인 마이그레이션에는 서비스 데이터 암호화 키가 필요 합니다. 이제 인벤토리의 각 어플라이언스에 대해 레코드에서이 키를 검색 하는 것이 좋습니다.

레코드에서 키를 찾을 수 없는 경우 어플라이언스에서 키를 검색할 수 있습니다. 각 어플라이언스에는 고유한 암호화 키가 있습니다. 키를 검색 하려면:

* Azure Portal를 통해 Microsoft Azure 지원 요청을 파일에 포함 합니다. 요청 내용에는 StorSimple 장치 일련 번호와 "서비스 데이터 암호화 키를 검색 하는 요청"이 있어야 합니다.
* StorSimple 지원 엔지니어가 화면 공유 모임 요청을 사용자에 게 연락 합니다.
* 회의가 시작 되기 전에 [직렬 콘솔](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) 또는 [원격 PowerShell 세션](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)을 통해 StorSimple 어플라이언스에 연결 해야 합니다.

> [!CAUTION]
> StorSimple 어플라이언스에 연결 하는 방법을 결정할 때 다음 사항을 고려 하세요.
>
> * HTTPS 세션을 통해 연결 하는 것이 가장 안전 하 고 권장 되는 옵션입니다.
> * 디바이스 직렬 콘솔에 직접 연결하는 것은 안전하지만 네트워크 스위치를 통해 직렬 콘솔에 연결하는 것은 안전하지 않습니다.
> * HTTP 세션 연결은 옵션 이지만 *암호화 되지* 않습니다. 폐쇄 된 신뢰할 수 있는 네트워크 내에서 사용 하지 않는 한 권장 되지 않습니다.

### <a name="storsimple-volume-backups"></a>StorSimple 볼륨 백업

StorSimple은 볼륨 수준에서 차등 백업을 제공 합니다. Azure 파일 공유에도 공유 스냅숏 이라고 하는이 기능이 있습니다.

마이그레이션의 일환으로 백업을 이동할 의무가 있는지 결정 합니다.

> [!CAUTION]
> StorSimple 볼륨에서 백업을 마이그레이션해야 하는 경우 여기에서 중지 합니다.
>
> 현재 가장 최근의 볼륨 백업만 마이그레이션할 수 있습니다. 백업 마이그레이션에 대 한 지원은 2020 끝에 도착할 예정입니다. 지금 시작 하는 경우 나중에 백업을 "연결할" 수 없습니다. 이후 버전에서 백업은 azure 파일 공유를 대상으로 하 여 가장 오래 된 azure 파일 공유에 "다시 재생" 해야 합니다.

라이브 데이터만 마이그레이션하고 백업에 대 한 요구 사항이 없는 경우이 가이드에 따라 계속할 수 있습니다. 한 달 또는 두 번의 단기 백업 보존 요구 사항이 있는 경우 지금 마이그레이션을 계속 하 고 해당 기간 후에 StorSimple 리소스의 프로 비전을 해제할 수 있습니다. 이 접근 방식을 사용 하면 Azure 파일 공유 쪽에서 필요한 만큼의 백업 기록을 만들 수 있습니다. 두 시스템을 계속 실행 하는 경우에는 추가 비용이 적용 됩니다 .이 방법을 사용 하면 단기 백업 보존 보다 많은 시간이 필요한 경우에는이 방법을 고려 하지 않는 것이 좋습니다.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Azure 파일 공유에 기존 StorSimple 볼륨 매핑

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>저장소 계정 수

마이그레이션은 각각 적은 수의 Azure 파일 공유를 포함 하는 여러 저장소 계정을 배포 하는 이점을 누릴 수 있습니다.

여러 사용자 또는 응용 프로그램에서 사용 하는 파일 공유가 활발 하 게 활성화 되어 있으면 두 개의 Azure 파일 공유가 저장소 계정의 성능 제한에 도달할 수 있습니다. 이로 인해 모범 사례는 각 저장소 계정으로 마이그레이션하는 것입니다. 각 저장소 계정에는 각각 고유한 개별 파일 공유와 저장소 계정 당 셋 또는 세 개의 공유만 포함 되어 있습니다.

각각 하나의 파일 공유를 사용 하 여 저장소 계정을 배포 하는 것이 가장 좋습니다. 여러 Azure 파일 공유를 동일한 저장소 계정으로 풀링할 수 있습니다. 여기에는 보관 공유가 있습니다.

이러한 고려 사항은 Azure File Sync에 비해 Azure VM 또는 서비스를 통해 [직접 클라우드 액세스](#direct-share-access-vs-azure-file-sync) 에 더 적용 됩니다. 이러한 공유에만 Azure File Sync를 사용 하려는 경우 여러 개의 Azure storage 계정으로 그룹화 하는 것이 좋습니다. 또한 앱을 클라우드로 리프트 앤 시프트 하 여 파일 공유에 직접 액세스 하는 것이 좋습니다. 또는 더 높은 IOPS 및 처리량 번호를 사용 하 여 혜택을 얻을 수 있는 Azure 서비스 사용을 시작할 수 있습니다.

공유 목록을 만든 경우 각 공유를 상주할 저장소 계정에 매핑합니다.

> [!IMPORTANT]
> Azure 지역을 결정 하 고 각 저장소 계정 및 Azure File Sync 리소스가 선택한 지역과 일치 하는지 확인 합니다.

### <a name="phase-1-summary"></a>1 단계 요약

1 단계의 끝에서:

* StorSimple 장치 및 볼륨에 대 한 유용한 개요를 확인할 수 있습니다.
* 각 StorSimple 장치에 대 한 서비스 데이터 암호화 키를 검색 했기 때문에 데이터 변환 서비스가 클라우드의 StorSimple 볼륨에 액세스할 준비가 되었습니다.
* 마이그레이션해야 하는 볼륨에 대 한 계획과 볼륨을 적절 한 수의 Azure 파일 공유 및 저장소 계정에 매핑하는 방법도 있습니다.

> [!CAUTION]
> StorSimple 볼륨에서 백업을 마이그레이션해야 하는 경우 **여기에서 중지** 합니다.
>
> 이 마이그레이션 방식은 현재 백업을 마이그레이션할 수 없는 새 데이터 변환 서비스 기능에 의존 합니다. 백업 마이그레이션에 대 한 지원은 2020 끝에 도착할 예정입니다. 현재 라이브 데이터만 마이그레이션할 수 있습니다. 지금 시작 하는 경우 나중에 백업을 "연결할" 수 없습니다. Azure 파일 공유 스냅숏은 사이에 Azure 파일 공유를 사용 하 여 가장 오래 된 데이터부터 라이브 데이터까지 Azure 파일 공유에 "다시 재생" 해야 합니다.

라이브 데이터만 마이그레이션하고 백업에 대 한 요구 사항이 없는 경우이 가이드에 따라 계속할 수 있습니다.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>2 단계: Azure storage 및 마이그레이션 리소스 배포

이 섹션에서는 Azure에 필요한 여러 리소스 종류의 배포에 대 한 고려 사항을 설명 합니다. 일부는 데이터 사후 마이그레이션을 보유 하 고 일부는 마이그레이션 전용으로 필요 합니다. 배포 계획을 종료할 때까지 리소스 배포를 시작 하지 마세요. 배포 된 후 Azure 리소스의 특정 측면을 변경 하는 것은 어려울 수 있습니다.

### <a name="deploy-storage-accounts"></a>저장소 계정 배포

여러 Azure storage 계정을 배포 해야 할 수도 있습니다. 각 항목에는이 문서의 이전 섹션에서 완료 한 배포 계획에 따라 적은 수의 Azure 파일 공유가 포함 됩니다. Azure Portal로 이동 하 여 [계획 된 저장소 계정을 배포](../common/storage-account-create.md#create-a-storage-account)합니다. 새 저장소 계정에 대해 다음과 같은 기본 설정을 준수 하는 것이 좋습니다.

#### <a name="subscription"></a>Subscription

StorSimple 배포에 사용한 것과 동일한 구독 또는 다른 구독을 사용할 수 있습니다. 유일한 제한 사항은 구독이 StorSimple 구독과 동일한 Azure Active Directory 테 넌 트에 있어야 한다는 것입니다. 마이그레이션을 시작 하기 전에 StorSimple 구독을 올바른 테 넌 트로 이동 하는 것이 좋습니다. 전체 구독만 이동할 수 있습니다. 개별 StorSimple 리소스는 다른 테 넌 트 또는 구독으로 이동할 수 없습니다.

#### <a name="resource-group"></a>Resource group

리소스 그룹은 리소스 구성과 관리 관리 권한을 지원 합니다. [Azure에서 리소스 그룹](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)에 대해 자세히 알아보세요.

#### <a name="storage-account-name"></a>스토리지 계정 이름

저장소 계정의 이름은 URL의 일부가 되 고 특정 문자 제한이 있습니다. 명명 규칙에서 저장소 계정 이름은 전 세계에서 고유 해야 하 고, 소문자와 숫자만 허용 하 고, 3 자에서 24 자 사이 여야 하며, 하이픈 또는 밑줄과 같은 특수 문자를 허용 하지 않는 것을 고려해 야 합니다. 자세한 내용은 [Azure storage 리소스 명명 규칙](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)을 참조 하세요.

#### <a name="location"></a>위치

저장소 계정의 위치 또는 Azure 지역은 매우 중요 합니다. Azure File Sync를 사용 하는 경우 모든 저장소 계정은 저장소 동기화 서비스 리소스와 동일한 지역에 있어야 합니다. 사용자가 선택 하는 Azure 지역은 로컬 서버 및 사용자에 게 가까워야 합니다. 리소스를 배포한 후에는 해당 지역을 변경할 수 없습니다.

StorSimple 데이터 (저장소 계정)가 현재 있는 다른 지역을 선택할 수 있습니다.

> [!IMPORTANT]
> 현재 StorSimple 저장소 계정 위치에서 다른 지역을 선택 하는 경우 마이그레이션 중에 [송신 요금이 적용 됩니다](https://azure.microsoft.com/pricing/details/bandwidth) . 데이터는 StorSimple 지역을 떠나 새 저장소 계정 영역을 입력 합니다. 동일한 Azure 지역 내에서 유지 하는 경우 대역폭 요금이 적용 되지 않습니다.

#### <a name="performance"></a>성능

Azure 파일 공유 또는 표준 저장소에 대해 premium storage (SSD)를 선택 하는 옵션이 있습니다. Standard storage [에는 파일 공유에 대 한 여러 계층이](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share)포함 되어 있습니다. Standard storage는 StorSimple에서 마이그레이션하는 대부분의 고객에 게 적합 한 옵션입니다.

아직 확실 하지 않나요?

* [프리미엄 Azure 파일 공유의 성능이](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)필요한 경우 premium storage를 선택 합니다.
* 핫 데이터 및 보관 데이터를 포함 하는 범용 파일 서버 작업에 대 한 표준 저장소를 선택 합니다. 또한 클라우드의 공유에서 유일한 작업을 Azure File Sync 하는 경우 standard storage를 선택 합니다.

#### <a name="account-kind"></a>계정 종류

* Standard storage의 경우 *StorageV2 (범용 v2)* 를 선택 합니다.
* 프리미엄 파일 공유의 경우 *FileStorage* 를 선택 합니다.

#### <a name="replication"></a>복제

사용할 수 있는 몇 가지 복제 설정이 있습니다. 다른 복제 유형에 대해 자세히 알아보세요.

다음 두 옵션 중 하나를 선택 합니다.

* *LRS (로컬 중복 저장소)*.
* 모든 Azure 지역에서 사용할 수 없는 *ZRS (영역 중복 저장소)*.

> [!NOTE]
> LRS 및 ZRS 중복 유형만 대용량 100 TiB Azure 파일 공유와 호환 됩니다.

모든 변형의 GRS (지역 중복 저장소)는 현재 지원 되지 않습니다. 나중에 중복성 유형을 전환 하 고 Azure에 대 한 지원이 도착할 때 GRS로 전환할 수 있습니다.

#### <a name="enable-100-tib-capacity-file-shares"></a>100-TiB 파일 공유 사용

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="저장소 계정을 만들기 위한 Azure Portal의 고급 탭을 표시 하는 이미지입니다.":::
    :::column-end:::
    :::column:::
        Azure Portal의 새 저장소 계정 마법사의 **고급** 섹션에서이 저장소 계정에 대 한 **대량 파일 공유** 지원을 사용 하도록 설정할 수 있습니다. 이 옵션을 사용할 수 없는 경우 잘못 된 중복 유형을 선택 했을 가능성이 높습니다. 이 옵션을 사용할 수 있게 하려면 LRS 또는 ZRS만 선택 해야 합니다.
    :::column-end:::
:::row-end:::

100 용량 TiB 파일 공유를 옵트인 하면 다음과 같은 여러 이점이 있습니다.

* 더 작은 5 TiB 용량 파일 공유에 비해 성능이 크게 증가 합니다 (예: IOPS 10 배).
* 마이그레이션이 훨씬 더 빨리 완료 됩니다.
* 파일 공유에 마이그레이션할 데이터를 모두 저장할 수 있는 충분 한 용량이 있는지 확인 합니다.
* 향후 성장을 다룹니다.

### <a name="azure-file-shares"></a>Azure 파일 공유

저장소 계정을 만든 후 저장소 계정의 **파일 공유** 섹션으로 이동 하 여 1 단계의 마이그레이션 계획에 따라 적절 한 수의 Azure 파일 공유를 배포 합니다. Azure에서 새 파일 공유에 대 한 다음 기본 설정을 준수 하는 것이 좋습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="새 파일 공유 UI를 보여 주는 Azure Portal 스크린샷.":::
    :::column-end:::
    :::column:::
        </br>**Name**</br>소문자, 숫자 및 하이픈만 사용할 수 있습니다.</br></br>**할당량**</br>여기에서 할당량은 Windows Server 인스턴스의 SMB 하드 할당량과 비교할 수 있습니다. 할당량에 도달 하면 마이그레이션 및 기타 서비스가 실패 하기 때문에 여기서 할당량을 설정 하지 않는 것이 좋습니다.</br></br>**계층**</br>새 파일 공유에 대해 **최적화 된 트랜잭션** 을 선택 합니다. 마이그레이션 중에는 많은 트랜잭션이 발생 합니다. 계층을 나중에 작업에 가장 적합 한 계층으로 변경 하는 것이 더 비용 효율적입니다.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple 데이터 관리자

마이그레이션 작업을 포함 하는 Azure 리소스를 **StorSimple Data Manager** 이라고 합니다. **새 리소스** 를 선택 하 고 검색 합니다. 그런 다음 **만들기** 를 선택합니다.

이 임시 리소스는 오케스트레이션에 사용 됩니다. 마이그레이션이 완료 된 후 프로 비전을 해제 합니다. StorSimple 저장소 계정과 동일한 구독, 리소스 그룹 및 지역에 배포 되어야 합니다.

### <a name="azure-file-sync"></a>Azure 파일 동기화

Azure File Sync를 사용 하면 자주 액세스 하는 파일의 온-프레미스 캐싱을 추가할 수 있습니다. StorSimple의 캐싱 기능과 마찬가지로, Azure File Sync 클라우드 계층화 기능은 Windows Server 인스턴스와 다중 사이트 동기화에서 사용 가능한 캐시 용량에 대해 향상 된 제어와 함께 로컬 액세스 대기 시간을 제공 합니다. 온-프레미스 캐시가 목표 인 경우 로컬 네트워크에서 충분 한 직접 연결 저장소 용량을 사용 하 여 Windows Server VM (물리적 서버 및 장애 조치 (failover) 클러스터도 지원 됨)을 준비 합니다.

> [!IMPORTANT]
> Azure File Sync를 아직 설정 하지 마세요. 공유 마이그레이션이 완료 된 후 Azure File Sync를 설정 하는 것이 가장 좋습니다. Azure File Sync 배포는 마이그레이션의 4 단계 이전에 시작할 필요가 없습니다.

### <a name="phase-2-summary"></a>2 단계 요약

2 단계를 완료 하면 저장소 계정 및 모든 Azure 파일 공유를 배포 합니다. StorSimple Data Manager 리소스도 있습니다. 마이그레이션 작업을 구성할 때 3 단계에서 후자를 사용 합니다.

## <a name="phase-3-create-and-run-a-migration-job"></a>3 단계: 마이그레이션 작업 만들기 및 실행

이 섹션에서는 마이그레이션 작업을 설정 하는 방법을 설명 하 고, 선택한 대상 Azure 파일 공유에 복사 해야 하는 StorSimple 볼륨의 디렉터리를 신중 하 게 매핑합니다. 시작 하려면 StorSimple Data Manager으로 이동 하 여 메뉴에서 **작업 정의** 를 찾고 **+ 작업 정의** 를 선택 합니다. 대상 저장소 유형은 기본 **Azure 파일 공유** 입니다.

![StorSimple 8000 시리즈 마이그레이션 작업 유형입니다.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "작업 정의의 스크린샷은 작업 유형을 묻는 새 작업 정의 대화 상자를 열고 파일 공유 또는 blob 컨테이너에 복사를 Azure Portal 합니다.")

> [!IMPORTANT]
> 마이그레이션 작업을 실행 하기 전에 StorSimple 볼륨의 자동으로 예약 된 백업을 모두 중지 합니다.

:::row:::
    :::column:::
        ![StorSimple 8000 시리즈 마이그레이션 작업](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "데이터 변환 서비스 작업에 대 한 새 작업 생성 양식의 스크린샷")
    :::column-end:::
    :::column:::
        **작업 정의 이름**</br>이 이름은 이동 하는 파일 집합을 나타내야 합니다. Azure 파일 공유와 비슷한 이름을 제공 하는 것이 좋습니다. </br></br>**작업이 실행 되는 위치**</br>지역을 선택 하는 경우 StorSimple 저장소 계정과 동일한 지역을 선택 하거나, 사용할 수 없는 경우 해당 지역을 가까운 지역으로 선택 해야 합니다. </br></br><h3>원본</h3>**원본 구독**</br>StorSimple Device Manager 리소스를 저장 하는 구독을 선택 합니다. </br></br>**StorSimple 리소스**</br>기기가 등록 된 Device Manager StorSimple을 선택 합니다. </br></br>**서비스 데이터 암호화 키**</br>레코드에서 키를 찾을 수 없는 경우 [이 문서의 이전 섹션](#storsimple-service-data-encryption-key) 을 확인 하세요. </br></br>**디바이스**</br>마이그레이션하려는 볼륨을 보유 하는 StorSimple 장치를 선택 합니다. </br></br>**볼륨**</br>원본 볼륨을 선택 합니다. 나중에 전체 볼륨 또는 하위 디렉터리를 대상 Azure 파일 공유로 마이그레이션할 것인지 결정 합니다. </br></br><h3>Target</h3>구독, 저장소 계정 및 Azure 파일 공유를이 마이그레이션 작업의 대상으로 선택 합니다.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 최신 볼륨 백업은 마이그레이션을 수행 하는 데 사용 됩니다. 볼륨 백업이 하나 이상 존재 하는지 확인 하거나 작업이 실패 합니다. 또한 최신 백업에 대 한 델타를 최대한 작은 라이브 공유로 유지 해야 합니다. 방금 만든 작업을 실행 *하기 전에* 다른 볼륨 백업을 수동으로 트리거하거나 완료 해야 할 수 있습니다.

### <a name="directory-mapping"></a>디렉터리 매핑

디렉터리 매핑은 마이그레이션 작업에 대 한 선택 사항입니다. 섹션을 비워 두면 StorSimple 볼륨의 루트에 있는 *모든* 파일 및 폴더가 대상 Azure 파일 공유의 루트로 이동 됩니다. 대부분의 경우 Azure 파일 공유에 전체 볼륨의 콘텐츠를 저장 하는 것이 가장 좋은 방법이 아닙니다. Azure의 여러 파일 공유에서 볼륨 콘텐츠를 분할 하는 것이 좋습니다. 계획을 아직 만들지 않은 경우 먼저 [Azure 파일 공유에 StorSimple 볼륨 매핑](#map-your-existing-storsimple-volumes-to-azure-file-shares) 을 참조 하세요.

마이그레이션 계획의 일부로 StorSimple 볼륨의 폴더를 여러 Azure 파일 공유로 분할 해야 한다고 결정 했을 수 있습니다. 이 경우 다음으로 분할을 수행할 수 있습니다.

1. 여러 작업을 정의 하 여 한 볼륨의 폴더를 마이그레이션합니다. 각에는 동일한 StorSimple 볼륨 원본이 있지만 Azure 파일 공유는 대상과 다릅니다.
1. 작업 생성 양식의 **디렉터리 매핑** 섹션을 사용 하 고 특정 [매핑 의미 체계](#semantic-elements)에 따라 StorSimple 볼륨의 폴더를 지정 된 파일 공유로 마이그레이션해야 하는 것을 정확 하 게 지정 합니다.

> [!IMPORTANT]
> 양식이 제출 되 면이 양식의 경로 및 매핑 식의 유효성을 검사할 수 없습니다. 매핑이 잘못 지정 된 경우 작업이 완전히 실패 하거나 바람직하지 않은 결과가 발생할 수 있습니다. 이 경우 일반적으로 Azure 파일 공유를 삭제 하 고 다시 만든 다음 공유에 대 한 새 마이그레이션 작업에서 매핑 문을 수정 하는 것이 가장 좋습니다. 고정 매핑 문으로 새 작업을 실행 하면 생략 된 폴더를 수정 하 여 기존 공유에 가져올 수 있습니다. 그러나 경로 맞춤법 오류로 인해 생략 된 폴더만 이러한 방식으로 해결할 수 있습니다.

#### <a name="semantic-elements"></a>의미 체계 요소

매핑은 왼쪽에서 오른쪽: [\source path] \> [\source path]로 표시 됩니다.

|의미 문자          | 의미  |
|:---------------------------|:---------|
| **\\**                     | 루트 수준 표시기입니다.       |
| **\>**                     | [원본] 및 [대상-매핑] 연산자가 있습니다.     |
|**\|** 또는 RETURN (새 줄) | 두 폴더 매핑 명령의 구분 기호입니다. </br>또는이 문자를 생략 하 고 **Enter 키** 를 선택 하 여 한 줄에 다음 매핑 식을 가져올 수 있습니다.        |

### <a name="examples"></a>예
*사용자 데이터* 폴더의 콘텐츠를 대상 파일 공유의 루트로 이동 합니다.
``` console
\User data > \
```
대상 파일 공유에서 전체 볼륨 내용을 새 경로로 이동 합니다.
``` console
\ > \Apps\HR tracker
```
대상 파일 공유에서 원본 폴더 내용을 새 경로로 이동 합니다.
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
여러 원본 위치를 새 디렉터리 구조로 정렬 합니다.
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>의미 체계 규칙

* 항상 루트 수준에 상대적인 폴더 경로를 지정 합니다.
* 루트 수준 표시기 ""를 사용 하 여 각 폴더 경로를 시작 \\ 합니다.
* 드라이브 문자를 포함 하지 않습니다.
* 여러 경로를 지정 하는 경우 원본 또는 대상 경로는 겹칠 수 없습니다.</br>
   잘못 된 원본 경로 중복 예:</br>
    *\\folder\1 > \\ 폴더*</br>
    *\\폴더 \\ 1 \\ 2 > \\ folder2*</br>
   잘못 된 대상 경로 중복 예:</br>
   *\\폴더 > \\*</br>
   *\\folder2 > \\*</br>
* 존재 하지 않는 소스 폴더는 무시 됩니다.
* 대상에 없는 폴더 구조가 생성 됩니다.
* Windows와 마찬가지로 폴더 이름도 대/소문자를 구분 하지 않지만 대/소문자를 유지 합니다.

> [!NOTE]
> *\System 볼륨 정보* 폴더의 내용과 StorSimple 볼륨의 *$Recycle* 는 변환 작업을 통해 복사 되지 않습니다.

### <a name="phase-3-summary"></a>3 단계 요약

3 단계를 마치면 StorSimple 볼륨에서 Azure 파일 공유로 데이터 변환 서비스 작업을 실행 하 게 됩니다. 이제 공유에 대 한 Azure File Sync 설정 (공유에 대 한 마이그레이션 작업이 완료 된 후) 또는 정보 근로자와 앱에 대 한 공유 액세스를 Azure 파일 공유로 전송 하는 방법에 집중할 수 있습니다.

## <a name="phase-4-access-your-azure-file-shares"></a>4 단계: Azure 파일 공유에 액세스

Azure 파일 공유에 액세스 하는 두 가지 주요 전략은 다음과 같습니다.

* **Azure File Sync** : 온-프레미스 Windows Server 인스턴스에 [Azure File Sync를 배포](#deploy-azure-file-sync) 합니다. Azure File Sync에는 StorSimple과 마찬가지로 로컬 캐시의 모든 이점이 있습니다.
* **직접 공유-액세스** : [직접 공유 액세스를 배포](#deploy-direct-share-access)합니다. 지정 된 Azure 파일 공유에 대 한 액세스 시나리오에서 로컬 캐싱을 활용 하지 않거나 더 이상 온-프레미스 Windows Server 인스턴스를 호스트할 수 없는 경우이 전략을 사용 합니다. 여기서 사용자와 앱은 SMB 프로토콜을 통해 SMB 공유에 계속 액세스 합니다. 이러한 공유는 더 이상 온-프레미스 서버에 없지만 클라우드에서 직접 사용할 수 있습니다.

이 가이드의 [1 단계](#phase-1-prepare-for-migration) 에서 사용자에 게 가장 적합 한 옵션을 이미 결정 해야 합니다.

이 섹션의 나머지 부분에서는 배포 지침을 중점적으로 설명 합니다.

### <a name="deploy-azure-file-sync"></a>Azure 파일 동기화 배포

Azure File Sync의 일부를 배포할 때입니다.

1. Azure File Sync 클라우드 리소스를 만듭니다.
1. 온-프레미스 서버에 Azure File Sync 에이전트를 배포 합니다.
1. 클라우드 리소스에 서버를 등록 합니다.

아직 동기화 그룹을 만들지 마세요. Azure 파일 공유와의 동기화 설정은 Azure 파일 공유에 대 한 마이그레이션 작업이 완료 된 후에만 수행 해야 합니다. 마이그레이션을 완료 하기 전에 Azure File Sync 사용을 시작한 경우에는 중단을 시작 해야 하는 시기를 쉽게 알 수 없으므로 마이그레이션을 불필요 하 게 어렵습니다.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure File Sync 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 마이그레이션이 완료 된 후 데이터가 있는 Azure 지역을 변경 하려면이 마이그레이션의 대상 저장소 계정과 동일한 지역에 저장소 동기화 서비스를 배포 합니다.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>온-프레미스 Windows Server 인스턴스 배포

* Windows Server 2019 (최소 2012R2)를 가상 컴퓨터 또는 물리적 서버로 만듭니다. Windows Server 장애 조치 (failover) 클러스터도 지원 됩니다. StorSimple 8100 또는 8600 서버 앞단에를 다시 사용 하지 마세요.
* 직접 연결 된 저장소를 프로 비전 하거나 추가 합니다. 네트워크에 연결 된 저장소는 지원 되지 않습니다.

새 Windows Server 인스턴스에 StorSimple 8100 또는 8600 어플라이언스에서 로컬로 사용할 수 있는 것 보다 같거나 더 많은 저장소를 제공 하는 것이 가장 좋습니다. StorSimple 어플라이언스를 사용한 것과 같은 방식으로 Windows Server 인스턴스를 사용 합니다. 기기와 동일한 저장소 크기를 갖는 경우 캐싱 환경은 동일 하지 않은 경우 유사 해야 합니다. Windows Server 인스턴스에서 저장소를 추가 하거나 제거할 수 있습니다. 이 기능을 사용 하면 로컬 볼륨 크기와 캐싱에 사용할 수 있는 로컬 저장소의 크기를 조정할 수 있습니다.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>파일 동기화에 대 한 Windows Server 인스턴스 준비

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Windows Server 인스턴스에서 Azure File Sync 구성

이 프로세스를 위해 등록 된 온-프레미스 Windows Server 인스턴스가 준비 되어 인터넷에 연결 되어 있어야 합니다.

> [!IMPORTANT]
> 계속 하기 전에 Azure 파일 공유로 파일 및 폴더의 StorSimple 마이그레이션을 완료 해야 합니다. 파일 공유에 대 한 변경 내용이 더 이상 없는지 확인 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 클라우드 계층화를 사용 하도록 설정 해야 합니다. 클라우드 계층화는 로컬 서버에서 클라우드에 저장 된 것 보다 저장소 용량을 줄일 수 있도록 하는 Azure File Sync 기능으로, 전체 네임 스페이스를 사용할 수 있습니다. 로컬에서 흥미로운 데이터는 빠른 로컬 액세스 성능을 위해 로컬에도 캐시 됩니다. 이 단계에서 클라우드 계층화를 켜는 또 다른 이유는이 단계에서 파일 콘텐츠를 동기화 하지 않으려고 한다는 것입니다. 지금은 네임 스페이스만 이동 해야 합니다.

### <a name="deploy-direct-share-access"></a>직접 공유-액세스 배포

:::row:::
    :::column:::
        [![Azure 파일 공유를 정보 근로자 및 앱에 직접 안전 하 게 노출 하는 방법에 대 한 단계별 가이드 및 데모-클릭 하 여 재생 합니다.](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        이 비디오는 5 가지 간단한 단계로 Azure 파일 공유를 정보 근로자 및 앱에 직접 안전 하 게 노출 하는 방법에 대 한 가이드 및 데모입니다.</br>
        비디오에서는 다음 항목에 대 한 전용 설명서를 참조 합니다.

* [ID 개요](storage-files-active-directory-overview.md)
* [저장소 계정에 도메인 가입 하는 방법](storage-files-identity-auth-active-directory-enable.md)
* [Azure 파일 공유에 대 한 네트워킹 개요](storage-files-networking-overview.md)
* [공용 및 개인 끝점을 구성 하는 방법](storage-files-networking-endpoints.md)
* [S2S VPN을 구성 하는 방법](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN을 구성 하는 방법](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN을 구성 하는 방법](storage-files-configure-p2s-vpn-linux.md)
* [DNS 전달을 구성 하는 방법](storage-files-networking-dns.md)
* [DFS 구성-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4 단계 요약

이 단계에서는 StorSimple Data Manager에서 여러 데이터 변환 서비스 작업을 만들어 실행 했습니다. 이러한 작업은 파일 및 폴더를 Azure 파일 공유로 마이그레이션 했습니다. 또한 직접 공유 액세스를 위해 네트워크 및 저장소 계정을 Azure File Sync 하거나 준비 했습니다.

## <a name="phase-5-user-cut-over"></a>5 단계: 사용자 잘림

이 단계는 마이그레이션을 래핑합니다.

* 가동 중지 시간을 계획 하세요.
* 3 단계에서 데이터 변환 작업이 실행 되는 동안 StorSimple 쪽에서 생성 된 사용자 및 앱에 대 한 변경 내용을 파악 합니다.
* 사용자가 직접 공유 액세스를 통해 Azure File Sync 또는 Azure 파일 공유를 사용 하 여 새 Windows Server 인스턴스로 장애 조치 (failover) 합니다.

### <a name="plan-your-downtime"></a>가동 중지 시간 계획

이 마이그레이션 방법에는 사용자 및 앱에 대 한 약간의 가동 중지 시간이 필요 합니다. 목표는 가동 중지 시간을 최소로 유지 하는 것입니다. 다음 사항을 고려할 수 있습니다.

* 데이터 변환 작업을 실행 하는 동안 StorSimple 볼륨을 사용할 수 있도록 유지 합니다.
* 공유에 대 한 데이터 마이그레이션 작업의 실행을 완료 한 후에는 StorSimple 볼륨이 나 공유에서 사용자 액세스 (최소한 쓰기 권한)를 제거 해야 합니다. 최종 RoboCopy는 Azure 파일 공유를 파악 합니다. 그런 다음 사용자를 잘라낼 수 있습니다. RoboCopy를 실행 하는 위치는 Azure File Sync를 사용 하도록 선택 했는지 아니면 직접 공유 액세스를 사용 하도록 선택 했는지에 따라 달라 집니다. RoboCopy의 예정 된 섹션에서는 해당 주제에 대해 설명 합니다.
* RoboCopy를 성공적으로 완료 한 후에는 Azure 파일 공유를 사용 하 여 사용자에 게 새 위치를 표시 하 고, Azure File Sync를 사용 하 여 Windows Server 인스턴스의 SMB 공유를 제공할 준비가 된 것입니다. 일반적으로 DFS N 배포를 통해 신속 하 고 효율적으로 신속 하 게 해결할 수 있습니다. 기존 공유 주소를 일관 되 게 유지 하 고 마이그레이션된 파일 및 폴더를 포함 하는 새 위치를 다시 가리킵니다.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>네임 스페이스가 서버와 완전히 동기화 된 시기 확인

Azure 파일 공유에 대 한 Azure File Sync를 사용 하는 경우 로컬 RoboCopy를 시작 *하기 전에* 전체 네임 스페이스가 서버에 다운로드를 완료 했는지 확인 하는 것이 중요 합니다. 네임 스페이스를 다운로드 하는 데 걸리는 시간은 Azure 파일 공유의 항목 수에 따라 달라 집니다. 네임 스페이스가 서버에 완전히 도달 했는지 여부를 확인 하는 방법에는 두 가지가 있습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal를 사용 하 여 네임 스페이스가 완전히 도착 한 시간을 확인할 수 있습니다.

* Azure Portal에 로그인 하 고 동기화 그룹으로 이동 합니다. 동기화 그룹 및 서버 끝점의 동기화 상태를 확인 합니다.
* 흥미로운 방향은 다운로드입니다. 서버 끝점이 새로 프로 비전 된 경우 **초기 동기화** 가 표시 되며,이는 네임 스페이스를 계속 사용할 수 있음을 나타냅니다.
이러한 변경 내용을 적용 한 후에는 **초기 동기화** 가 수행 되지만 네임 스페이스는 서버에 완전히 채워집니다. 이제 로컬 RoboCopy를 진행할 수 있습니다.

#### <a name="windows-server-event-viewer"></a>Windows Server 이벤트 뷰어

Windows Server 인스턴스에서 이벤트 뷰어를 사용 하 여 네임 스페이스가 완전히 도착 한 시기를 확인할 수도 있습니다.

1. **이벤트 뷰어** 를 열고 **응용 프로그램 및 서비스** 로 이동 합니다.
1. 로 이동 하 여 **Microsoft\FileSync\Agent\Telemetry** 를 엽니다.
1. 완료 된 동기화 세션에 해당 하는 최신 **이벤트 9102** 을 찾습니다.
1. **세부 정보** 를 선택 하 고 **Syncdirection** 값이 **다운로드** 되는 이벤트를 확인 하 고 있는지 확인 합니다.
1. 네임 스페이스에서 서버에 대 한 다운로드를 완료 한 경우에는 **시나리오** , 값 **FullGhostedSync** 및 **HResult** 0을 포함 하는 단일 이벤트가 발생  =  **0** 합니다.
1. 해당 이벤트를 놓친 경우 **syncdirection** **9102 events**  =  **다운로드** 및 **시나리오**  =  **"RegularSync"** 를 사용 하 여 다른 9102 이벤트를 찾을 수도 있습니다. 이러한 이벤트 중 하나를 찾으면 동기화가 완료 되었는지 여부에 관계 없이 네임 스페이스의 다운로드 및 동기화가 일반 동기화 세션으로 진행 되었음을 알 수 있습니다.

### <a name="a-final-robocopy"></a>최종 RoboCopy

이 시점에서 온-프레미스 Windows Server 인스턴스와 StorSimple 8100 또는 8600 어플라이언스 간에 차이가 있습니다.

1. 마이그레이션이 진행 되는 동안 StorSimple 쪽에서 사용자 또는 앱이 생성 한 변경 내용을 파악 해야 합니다.
1. Azure File Sync를 사용 하는 경우: StorSimple 어플라이언스에는 현재 로컬로 저장 된 파일 콘텐츠가 없는 네임 스페이스를 포함 하는 Windows Server 인스턴스와 채워진 캐시가 있습니다. 최종 RoboCopy는 로컬 캐시 된 파일 콘텐츠를 사용 가능 하 게 설정 하 고 Azure File Sync 서버에 맞게 조정 하 여 로컬 Azure File Sync 캐시를 신속 하 게 시작할 수 있습니다.
1. 일부 파일은 잘못 된 문자로 인해 데이터 변환 작업에 의해 남아 있을 수 있습니다. 이 경우 Azure File Sync 사용 가능한 Windows Server 인스턴스로 복사 합니다. 나중에이를 조정 하 여 동기화 할 수 있습니다. 특정 공유에 대해 Azure File Sync를 사용 하지 않는 경우 StorSimple 볼륨에서 잘못 된 문자가 포함 된 파일의 이름을 바꾸는 것이 좋습니다. 그런 다음 Azure 파일 공유에 대해 직접 RoboCopy를 실행 합니다.

> [!WARNING]
> 서버에 Azure 파일 공유에 대 한 네임 스페이스가 완전히 다운로드 되기 전에는 RoboCopy를 시작 하면 *안* 됩니다. 자세한 내용은 [네임 스페이스가 서버에 완전히 다운로드 된 시기 결정](#determine-when-your-namespace-has-fully-synced-to-your-server)을 참조 하세요.

 마이그레이션 작업을 마지막으로 실행 한 후 변경 된 파일 및 이전에 이러한 작업을 통해 이동 하지 않은 파일을 복사 하려고 합니다. 마이그레이션이 완료 된 후 서버에서 나중에 이동 하지 않은 이유에 대 한 문제를 해결할 수 있습니다. 자세한 내용은 [Azure File Sync 문제 해결](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)을 참조 하세요.

RoboCopy에는 여러 매개 변수가 있습니다. 다음 예제에서는 완성 된 명령과 이러한 매개 변수를 선택 하는 이유 목록을 보여 줍니다.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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
      모든 파일 정보를 복사 합니다 (/COPY: DATSOU와 동일).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      디렉터리 복사본에 대 한 충실도 (기본값:/DCOPY: DA), 복사 플래그: D = 데이터, A = 특성, T = 타임 스탬프.
   :::column-end:::
:::row-end:::

RoboCopy 명령의 원본 및 대상 위치를 구성 하는 경우 원본 및 대상의 구조를 검토 하 여 일치 하는지 확인 해야 합니다. 마이그레이션 작업의 디렉터리 매핑 기능을 사용 하는 경우 루트 디렉터리 구조는 StorSimple 볼륨의 구조와 다를 수 있습니다. 이 경우 각 하위 디렉터리에 대해 하나씩 여러 RoboCopy 작업이 필요할 수 있습니다. 명령이 예상 대로 수행 되는 것이 확실 하지 않으면 */l* 매개 변수를 사용할 수 있습니다 .이 매개 변수는 실제로 변경 하지 않고 명령을 시뮬레이션 합니다.

이 RoboCopy 명령은/MIR을 사용 하므로 같은 (계층화 된 파일) 파일을 이동 하지 않습니다. 하지만 원본 및 대상 경로가 잘못 된 경우/MIR는 StorSimple 원본 경로에 없는 Windows Server 인스턴스 또는 Azure 파일 공유의 디렉터리 구조도 제거 합니다. 마이그레이션이 진행 되는 동안 수행 된 최신 변경 내용으로 마이그레이션된 콘텐츠를 업데이트 하는 목표를 달성 하기 위해 RoboCopy 작업에 정확히 일치 해야 합니다.

RoboCopy 로그 파일을 참조 하 여 파일이 남아 있는지 확인 하십시오. 문제가 있는 경우 수정 하 고 RoboCopy 명령을 다시 실행 하십시오. 관심 있는 파일이 나 폴더에 대 한 해결 되지 않은 문제를 해결 하기 전에 StorSimple 리소스의 프로 비전을 해제 하지 마십시오.

Azure File Sync를 사용 하 여 특정 Azure 파일 공유를 캐시 하지 않고 대신 직접 공유 액세스를 사용 하지 않는 경우:

1. [Azure 파일 공유](storage-how-to-use-files-windows.md#mount-the-azure-file-share) 를 로컬 Windows 컴퓨터에 네트워크 드라이브로 탑재 합니다.
1. StorSimple과 탑재 된 Azure 파일 공유 사이에서 RoboCopy를 수행 합니다. 파일이 복사 되지 않는 경우 StorSimple 쪽에서 이름을 수정 하 여 잘못 된 문자를 제거 합니다. 그런 다음, RoboCopy를 다시 시도 합니다. 이전에 나열 된 RoboCopy 명령은 StorSimple에 불필요 한 회수를 발생 시 키 지 않고 여러 번 실행할 수 있습니다.

### <a name="user-cut-over"></a>사용자 잘라내기

Azure File Sync 사용 하는 경우 StorSimple 볼륨에 있는 공유와 일치 하는 Azure File Sync 사용 가능한 Windows Server 인스턴스에서 SMB 공유를 만들어야 합니다. 이 단계를 앞으로 로드 하 고 이전에 시간이 손실 되지 않도록 할 수 있습니다. 그러나이 시점 전에는 Windows Server 인스턴스를 변경 하는 데 필요한 액세스 권한이 없습니다.

DFS N을 배포 하는 경우 DFN-Namespaces 새 서버 폴더 위치를 가리키도록 할 수 있습니다. DFS N이 배포 되지 않은 상태에서 8100 또는 8600 어플라이언스를 Windows Server 인스턴스와 로컬로 제어 되 하는 경우 해당 서버를 도메인에서 사용할 수 있습니다. 그런 다음 새 Azure File Sync 사용 Windows Server 인스턴스에 도메인 가입 합니다. 이 프로세스 중에는 사용자, 그룹 정책 및 스크립트에 대해 잘라내기를 투명 하 게 유지 하도록 서버에 이전 서버와 동일한 서버 이름과 공유 이름을 지정 합니다.

[DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)에 대해 자세히 알아보세요.

## <a name="deprovision"></a>프로비전 해제

리소스의 프로 비전을 해제 하면 해당 리소스와 해당 데이터의 구성에 대 한 액세스 권한이 손실 됩니다. 프로 비전 해제는 취소할 수 없습니다. 다음을 확인할 때까지 계속 진행 하지 마세요.

* 마이그레이션이 완료 되었습니다.
* 프로 비전을 해제할 StorSimple 파일, 폴더 또는 볼륨 백업에 대 한 종속성이 없습니다.

시작 하기 전에, 잠시 동안 프로덕션 환경에서 새 Azure File Sync 배포를 관찰 하는 것이 좋습니다. 이러한 시간을 사용 하면 발생할 수 있는 문제를 해결할 수 있습니다. 하루 이상 Azure File Sync 배포를 관찰 한 후에는 다음 순서로 리소스 프로 비전을 해제할 수 있습니다.

1. Azure Portal를 통해 StorSimple Data Manager 리소스의 프로 비전을 해제 합니다. 모든 DTS 작업이 함께 삭제 됩니다. 복사 로그는 쉽게 검색할 수 없습니다. 레코드에 중요 한 경우 프로 비전을 해제 하기 전에 검색 합니다.
1. StorSimple 물리적 어플라이언스가 마이그레이션 되었는지 확인 하 고 등록을 취소 합니다. 마이그레이션되지 않은 경우 계속 진행 하지 마세요. 필요에 따라 이러한 리소스를 프로 비전 해제 하는 경우 데이터 또는 해당 구성을 복구할 수 없습니다.<br>필요에 따라 어플라이언스의 데이터를 정리 하는 StorSimple 볼륨 리소스의 프로 비전을 해제할 수 있습니다. 이는 며칠 정도 걸릴 수 있으며, 어플라이언스의 데이터를 0 하기 **하지** 않습니다. 이 사항이 중요 한 경우 프로 비전 해제 된 리소스와 정책에 따라 별도로 디스크 제로화를 처리 합니다.
1. StorSimple Device Manager에 더 이상 등록 된 장치가 남아 있지 않으면 해당 Device Manager 리소스 자체를 계속 제거할 수 있습니다.
1. 이제 Azure에서 StorSimple 저장소 계정을 삭제 하는 시간입니다. 다시, 마이그레이션을 완료 했는지 확인 하 고, 마이그레이션을 완료 하 고 아무도이 데이터에 종속 되지 않도록 합니다.
1. 데이터 센터에서 StorSimple 물리적 어플라이언스를 분리 합니다.
1. StorSimple 어플라이언스를 소유 하 고 있는 경우 PC를 무료로 재생할 수 있습니다. 장치가 임대 된 경우 lessor에 게 알리고 장치를 적절 하 게 반환 합니다.

마이그레이션이 완료 되었습니다.

> [!NOTE]
> 여전히 궁금한 점이 있거나 문제가 발생 했나요?</br>
> 여기서 도와 드리겠습니다 AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>다음 단계

* [Azure File Sync: aka.ms/AFS](./storage-sync-files-planning.md)에 대해 자세히 알아보세요.
* [클라우드 계층화](storage-sync-cloud-tiering.md) 정책의 유연성을 이해 합니다.
* Azure 파일 공유에 대 한 [Azure Backup를 사용 하도록 설정](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) 하 여 스냅숏을 예약 하 고 백업 보존 일정을 정의 합니다.
* Azure Portal에서 일부 파일이 영구적으로 동기화 되지 않는 것을 확인 하는 경우 문제 [해결 가이드](storage-sync-files-troubleshoot.md) 를 검토 하 여 이러한 문제를 해결 합니다.