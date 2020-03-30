---
title: Azure 파일 공유로 마이그레이션
description: Azure 파일 공유로의 마이그레이션에 대해 알아보고 마이그레이션 가이드를 찾습니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247319"
---
# <a name="migrate-to-azure-file-shares"></a>Azure 파일 공유로 마이그레이션

이 문서에서는 Azure 파일 공유로 마이그레이션하는 기본 측면을 다룹니다.

이 문서에는 마이그레이션 기본 사항과 함께 개별화된 기존 마이그레이션 가이드 목록이 포함되어 있습니다. 이러한 마이그레이션 가이드는 파일을 Azure 파일 공유로 이동하는 데 도움이 되며 현재 데이터가 있는 위치와 이동할 계획인 배포 모델(클라우드 전용 또는 하이브리드)에 따라 구성됩니다.

## <a name="migration-basics"></a>마이그레이션 기본 사항

Azure에서 사용할 수 있는 여러 가지 유형의 클라우드 저장소가 있습니다. Azure로 파일을 마이그레이션하는 기본적인 측면은 데이터에 적합한 Azure 저장소 옵션을 확인하는 것입니다.

Azure 파일 공유는 범용 파일 데이터에 적합합니다. 온-프레미스 SMB 또는 NFS 공유를 사용하는 모든 것을 사용할 수 있습니다. [Azure 파일 동기화를](storage-sync-files-planning.md)사용하면 온-프레미스의 여러 Windows 서버에서 여러 Azure 파일 공유의 내용을 선택적으로 캐시할 수 있습니다.

현재 온-프레미스 서버에서 실행 중인 응용 프로그램이 있는 경우 응용 프로그램에 따라 Azure 파일 공유에 파일을 저장하는 것이 적합할 수 있습니다. Azure에서 실행되도록 응용 프로그램을 해제하고 Azure 파일 공유를 공유 저장소로 사용할 수 있습니다. 이 시나리오에 대 한 [Azure 디스크를](../../virtual-machines/windows/managed-disks-overview.md) 고려할 수도 있습니다. SMB 또는 컴퓨터 로컬 액세스에 의존하지 않는 클라우드 에서 응용 프로그램을 기반으로 하거나 데이터 또는 공유 액세스에 종속되지 않는 경우 [Azure Blob과](../blobs/storage-blobs-overview.md)같은 개체 저장소가 가장 적합한 경우가 많습니다.

모든 마이그레이션의 핵심은 파일을 현재 저장소 위치에서 Azure로 마이그레이션할 때 적용 가능한 모든 파일 충실도를 캡처하는 것입니다. 올바른 Azure 저장소를 선택하는 데 도움이 되는 것은 Azure 저장소 옵션에서 지원되는 충실도의 양과 시나리오에서 필요한 정도의 측면이기도 합니다. 범용 파일 데이터는 일반적으로 파일 메타데이터에 따라 달라집니다. 응용 프로그램 데이터가 아닐 수 있습니다. 파일에는 두 가지 기본 구성 요소가 있습니다.

- **데이터 스트림**: 파일의 데이터 스트림은 파일 콘텐츠를 저장합니다.
- **파일 메타데이터**: 파일 메타 데이터에는 몇 가지 하위 구성 요소가 있습니다.
   * 파일 특성: 예를 들어 읽기 전용입니다.
   * 파일 권한: *NTFS 권한* 또는 *파일 및 폴더 ACL이라고*합니다.
   * 타임스탬프: 특히 *생성* 및 *마지막 수정된* 타임스탬프입니다.
   * 대체 데이터 스트림: 더 많은 양의 비표준 속성을 저장할 수 있는 공간입니다.

따라서 마이그레이션에서 파일 충실도는 원본에 적용 가능한 모든 파일 정보를 저장하는 기능, 마이그레이션 도구로 전송하는 기능 및 마이그레이션의 대상 저장소에 저장할 수 있는 기능으로 정의할 수 있습니다.

마이그레이션이 가능한 한 원활하게 진행되도록 하려면 [필요에 가장 적합한 복사 도구를](#migration-toolbox) 식별하고 저장소 대상을 원본에 일치시킵니다.

이전 정보를 고려하면 Azure의 범용 파일에 대한 대상 저장소가 무엇인지 명확히 알 수 [있습니다.](storage-files-introduction.md) Azure Blob의 개체 저장소와 비교하여 파일 메타데이터는 Azure 파일 공유의 파일에 기본적으로 저장할 수 있습니다.

Azure 파일 공유는 파일 및 폴더 계층 구조도 유지합니다. 또한 다음 작업도 수행해야 합니다.
* NTFS 권한은 온-프레미스에 있는 파일 및 폴더에 저장할 수 있습니다.
* AD 사용자(또는 Azure AD DS 사용자)는 기본적으로 Azure 파일 공유에 액세스할 수 있습니다. 
    현재 ID를 사용하고 공유 권한과 파일 및 폴더 ACL을 기반으로 액세스 권한을 얻습니다. 사용자가 온-프레미스 파일 공유에 연결할 때와 달리 동작입니다.
*  대체 데이터 스트림은 현재 Azure 파일 공유의 파일에 저장할 수 없는 파일 충실도의 기본 측면입니다.
   Azure 파일 동기화가 관련된 경우 온-프레미스에서 보존됩니다.

* [Azure 파일 공유에 대한 AD 인증에 대해 자세히 알아보기](storage-files-identity-auth-active-directory-enable.md)
* [Azure 파일 공유에 대한 Azure Active Directory 도메인 서비스(AAD DS) 인증에 대해 자세히 알아보기](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>마이그레이션 가이드

다음 표에는 자세한 마이그레이션 가이드가 나와 있습니다.

다음을 통해 탐색합니다.
1. 파일이 현재 저장되어 있는 소스 시스템의 행을 찾습니다.
2. Azure File Sync를 사용하여 하나 이상의 Azure 파일 공유 온-프레미스의 콘텐츠를 캐시하는 하이브리드 배포를 대상으로 할지 또는 클라우드에서 직접 Azure 파일 공유를 사용할지 여부를 결정합니다. 결정을 반영하는 대상 열을 선택합니다.
3. 원본과 대상의 교차점 내에서 테이블 셀에는 사용 가능한 마이그레이션 시나리오가 나열됩니다. 그 중 하나를 선택하여 자세한 마이그레이션 가이드에 직접 연결합니다.

링크가 없는 시나리오에는 아직 게시된 마이그레이션 가이드가 없습니다. 이 표에서 업데이트를 확인하십시오. 사용 가능한 경우 새 가이드가 게시됩니다.

| 원본 | 대상: </br>하이브리드 배포 | 대상: </br>클라우드 전용 배포 |
|:---|:--|:--|
| | 공구 조합:| 공구 조합: |
| Windows Server 2012 R2 이상 | <ul><li>[Azure 파일 동기화](storage-sync-files-deployment-guide.md)</li><li>[Azure 파일 동기화 + 데이터 박스](storage-sync-offline-data-transfer.md)</li><li>저장소 마이그레이션 서비스 + Azure 파일 동기화</li></ul> | <ul><li>Azure 파일 동기화</li><li>Azure 파일 동기화 + 데이터 박스</li><li>저장소 마이그레이션 서비스 + Azure 파일 동기화</li><li>Robocopy</li></ul> |
| Windows 서버 2012 이상 | <ul><li>Azure 파일 동기화 + 데이터 박스</li><li>저장소 마이그레이션 서비스 + Azure 파일 동기화</li></ul> | <ul><li>저장소 마이그레이션 서비스 + Azure 파일 동기화</li><li>Robocopy</li></ul> |
| 네트워크 연결 스토리지(NAS) | <ul><li>[Azure 파일 동기화 + 로보카피](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| 리눅스 / 삼바 | <ul><li>[로보카피 + Azure 파일 동기화](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| 스토심플 8100 / 8600 | <ul><li>[Azure 파일 동기화 + 8020 가상 어플라이언스](storage-files-migration-storsimple-8000.md)</li></ul> | |
| 스토심플 1200 | <ul><li>[Azure 파일 동기화](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>마이그레이션 도구 상자

### <a name="file-copy-tools"></a>파일 복사 도구

몇 가지 Microsoft 및 비 Microsoft 파일 복사 도구를 사용할 수 있습니다. 마이그레이션 시나리오에 적합한 파일 복사 도구를 선택하려면 다음 세 가지 기본 질문을 고려해야 합니다.

* 복사 도구가 지정된 파일 복사본의 원본 및 대상 위치를 지원합니까? 
    * 소스 및 대상 저장소 위치를 오가는 네트워크 경로 및/또는 사용 가능한 프로토콜(예: REST/SMB/NFS)을 지원합니까?
* 복사 도구는 소스/대상 위치에서 지원하는 필요한 파일 충실도를 유지합니까? 경우에 따라 대상 저장소가 원본과 동일한 충실도를 지원하지 않는 경우도 있습니다. 이미 대상 저장소가 사용자의 요구에 충분하다는 결정을 내렸기 때문에 복사 도구는 대상 파일 충실도 기능과 일치하기만 하면 됩니다.
* 복사 도구에 마이그레이션 전략에 맞는 기능이 있습니까? 
    * 예를 들어 가동 중지 시간을 최소화할 수 있는 옵션이 있는지 생각해 보십시오. 좋은 질문은 : 사용자가 적극적으로 액세스 한 위치에 의해 동일한 복사본을 여러 번 실행할 수 있습니까? 이 경우 가동 중지 시간을 크게 줄일 수 있습니다. 전체 복사본을 보장하기 위해 소스 변경을 중지할 때만 복사본을 시작할 수 있는 상황과 비교합니다.

다음 표는 Microsoft 도구와 Azure 파일 공유에 대한 현재 적합성을 분류합니다.

| 권장 | 도구 | Azure 파일 공유 지원 | 파일 충실도 유지 |
| :-: | :-- | :---- | :---- |
|![예, 권장됩니다.](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | 지원됩니다. Azure 파일 공유를 네트워크 드라이브로 탑재할 수 있습니다. | 전체 충실도* |
|![예, 권장됩니다.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 파일 동기화 | 기본적으로 Azure 파일 공유에 통합됩니다. | 전체 충실도* |
|![예, 권장됩니다.](media/storage-files-migration-overview/circle-green-checkmark.png)| SMS(스토리지 마이그레이션 서비스) | 간접적으로 지원됩니다. Azure 파일 공유는 SMS 대상 서버의 네트워크 드라이브로 탑재할 수 있습니다. | 전체 충실도* |
|![완전히 권장되지 않습니다.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 데이터 박스 | 지원됩니다. | 메타데이터를 복사하지 않습니다. [Azure 파일 동기화 와 함께 사용할 수 있습니다.](storage-sync-offline-data-transfer.md) |
|![이 옵션은 사용하지 않는 것이 좋습니다.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|![이 옵션은 사용하지 않는 것이 좋습니다.](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|![이 옵션은 사용하지 않는 것이 좋습니다.](media/storage-files-migration-overview/circle-red-x.png)| Azure 데이터 팩터리 | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|||||

*\*전체 충실도: Azure 파일 공유 기능을 충족하거나 초과합니다.*

### <a name="migration-helper-tools"></a>마이그레이션 도우미 도구

이 섹션에서는 마이그레이션을 계획하고 실행하는 데 도움이 되는 도구가 나열됩니다.

* **로보카피, 마이크로소프트**

    파일 마이그레이션에 가장 적용 가능한 복사 도구 중 하나는 Microsoft Windows의 일부로 제공됩니다. 이 도구의 많은 옵션으로 인해 주요 [RoboCopy 설명서는](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 유용한 소스입니다.

* **트리 사이즈, JAM 소프트웨어 GmbH에서**

    Azure 파일 동기화는 주로 항목 수(파일 및 폴더)로 확장되며 총 TiB 양이 적습니다. 이 도구를 사용하여 Windows Server 볼륨의 파일 및 폴더 수를 확인할 수 있습니다. 또한 [Azure File Sync 배포](storage-sync-files-deployment-guide.md) 전에 큐브 뷰를 만드는 데 사용할 수 있지만 클라우드 계층이 종사하는 경우 항목 수뿐만 아니라 서버 캐시가 가장 많이 사용되는 디렉터리도 확인하려는 경우도 있습니다.
    이 도구(테스트된 버전 4.4.1)는 클라우드 계층화 파일과 호환됩니다. 정상적인 작동 중에 계층화 된 파일이 회수되지 않습니다.


## <a name="next-steps"></a>다음 단계

1. Azure 파일 공유(클라우드 전용 또는 하이브리드)의 배포를 위해 노력하는 계획을 만듭니다.
2. 사용 가능한 마이그레이션 가이드 목록을 검토하여 Azure 파일 공유의 원본 및 배포와 일치하는 자세한 가이드를 찾습니다.

이 문서에서 언급한 Azure Files 기술에 대한 자세한 정보가 있습니다.

* [Azure 파일 공유 개요](storage-files-introduction.md)
* [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
* [Azure 파일 동기화: 클라우드 계층화](storage-sync-cloud-tiering.md)
