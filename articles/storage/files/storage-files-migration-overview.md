---
title: Azure 파일 공유로 마이그레이션
description: Azure 파일 공유로의 마이그레이션에 대해 알아보고 마이그레이션 가이드를 찾습니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685982"
---
# <a name="migrate-to-azure-file-shares"></a>Azure 파일 공유로 마이그레이션

이 문서에서는 Azure 파일 공유로 마이그레이션하는 기본 측면을 다룹니다.

이 문서에는 마이그레이션 기본 사항과 마이그레이션 가이드 테이블이 포함되어 있습니다. 이 가이드는 파일을 Azure 파일 공유로 이동하는 데 도움이 됩니다. 가이드는 데이터가 어디에 있는지, 어떤 배포 모델(클라우드 전용 또는 하이브리드)에 따라 구성됩니다.

## <a name="migration-basics"></a>마이그레이션 기본 사항

Azure에는 사용 가능한 여러 유형의 클라우드 저장소가 있습니다. Azure로의 파일 마이그레이션의 기본 측면은 데이터에 적합한 Azure 저장소 옵션을 결정하는 것입니다.

[Azure 파일 공유는](storage-files-introduction.md) 범용 파일 데이터에 적합합니다. 이 데이터에는 온-프레미스 SMB 또는 NFS 공유를 사용하는 모든 것이 포함됩니다. [Azure 파일 동기화를](storage-sync-files-planning.md)사용하면 Windows Server 온-프레미스를 실행하는 서버에서 여러 Azure 파일 공유의 내용을 캐시할 수 있습니다.

현재 온-프레미스 서버에서 실행 중인 앱의 경우 Azure 파일 공유에 파일을 저장하는 것이 좋습니다. 앱을 Azure로 이동하고 Azure 파일 공유를 공유 저장소로 사용할 수 있습니다. 이 시나리오에 대 한 [Azure 디스크를](../../virtual-machines/windows/managed-disks-overview.md) 고려할 수도 있습니다.

일부 클라우드 앱은 SMB 또는 컴퓨터 로컬 데이터 액세스 또는 공유 액세스에 의존하지 않습니다. 이러한 앱의 경우 [Azure Blob과](../blobs/storage-blobs-overview.md) 같은 개체 저장소가 최선의 선택인 경우가 많습니다.

모든 마이그레이션의 핵심은 파일을 현재 저장소 위치에서 Azure로 이동할 때 적용 가능한 모든 파일 충실도를 캡처하는 것입니다. Azure 저장소 옵션이 지원하는 충실도와 시나리오에 필요한 정도도 올바른 Azure 저장소를 선택하는 데 도움이 됩니다. 범용 파일 데이터는 일반적으로 파일 메타데이터에 따라 달라집니다. 앱 데이터가 그렇지 않을 수 있습니다.

다음은 파일의 두 가지 기본 구성 요소입니다.

- **데이터 스트림**: 파일의 데이터 스트림은 파일 콘텐츠를 저장합니다.
- **파일 메타데이터**: 파일 메타데이터에 다음과 같은 하위 구성 요소가 있습니다.
   * 읽기 전용과 같은 파일 속성
   * *NTFS 권한* 또는 *파일 및 폴더 ACL이라고* 할 수 있는 파일 권한
   * 타임스탬프, 특히 생성 및 마지막으로 수정된 타임스탬프
   * 더 많은 양의 비표준 속성을 저장할 수 있는 공간인 대체 데이터 스트림

마이그레이션의 파일 충실도는 다음과 같은 기능으로 정의할 수 있습니다.

- 원본에 적용 가능한 모든 파일 정보를 저장합니다.
- 마이그레이션 도구를 사용하여 파일을 전송합니다.
- 마이그레이션의 대상 저장소에 파일을 저장합니다.

마이그레이션이 원활하게 진행되도록 하려면 [필요에 가장 적합한 복사 도구를](#migration-toolbox) 식별하고 저장소 대상을 원본에 일치시킵니다.

이전 정보를 고려하면 Azure의 범용 파일에 대한 대상 저장소가 [Azure 파일 공유임을](storage-files-introduction.md)알 수 있습니다.

Azure Blob의 개체 저장소와 달리 Azure 파일 공유는 기본적으로 파일 메타데이터를 저장할 수 있습니다. Azure 파일 공유는 파일 및 폴더 계층 구조, 특성 및 사용 권한도 유지합니다. NTFS 권한은 온-프레미스이므로 파일 및 폴더에 저장할 수 있습니다.

온-프레미스 도메인 컨트롤러인 Active Directory 사용자는 기본적으로 Azure 파일 공유에 액세스할 수 있습니다. Azure Active Directory 도메인 서비스(Azure AD DS)의 사용자도 마찬가지입니다. 각 ID는 공유 사용 권한 및 파일 및 폴더 ACL에 따라 액세스 권한을 얻기 위해 현재 ID를 사용합니다. 이 동작은 온-프레미스 파일 공유에 연결하는 사용자와 유사합니다.

대체 데이터 스트림은 현재 Azure 파일 공유의 파일에 저장할 수 없는 파일 충실도의 기본 측면입니다. Azure 파일 동기화를 사용할 때 온-프레미스에서 보존됩니다.

[Azure AD 인증](storage-files-identity-auth-active-directory-enable.md) 및 Azure 파일 공유에 대한 [Azure AD DS 인증에](storage-files-identity-auth-active-directory-domain-service-enable.md) 대해 자세히 알아봅니다.

## <a name="migration-guides"></a>마이그레이션 가이드

다음 표에는 자세한 마이그레이션 가이드가 나와 있습니다.

테이블 사용 방법:

1. 파일이 현재 저장되어 있는 소스 시스템의 행을 찾습니다.

1. 다음 목표 중 하나를 선택합니다.

   - Azure 파일 동기화를 사용하여 Azure 파일 공유 온-프레미스의 콘텐츠를 캐시하는 하이브리드 배포
   - 클라우드에서 Azure 파일 공유

   선택한 항목과 일치하는 대상 열을 선택합니다.

1. 원본과 대상의 교차점 내에서 테이블 셀에는 사용 가능한 마이그레이션 시나리오가 나열됩니다. 자세한 마이그레이션 가이드에 직접 링크할 하나를 선택합니다.

링크가 없는 시나리오에는 아직 게시된 마이그레이션 가이드가 없습니다. 이 표에서 업데이트를 확인하십시오. 새 가이드는 사용 가능한 경우 게시됩니다.

| 원본 | 대상: </br>하이브리드 배포 | 대상: </br>클라우드 전용 배포 |
|:---|:--|:--|
| | 공구 조합:| 공구 조합: |
| 윈도우 서버 2012 R2 이상 | <ul><li>[Azure 파일 동기화](storage-sync-files-deployment-guide.md)</li><li>[Azure 파일 동기화 및 Azure 데이터 상자](storage-sync-offline-data-transfer.md)</li><li>Azure 파일 동기화 및 저장소 마이그레이션 서비스</li></ul> | <ul><li>Azure 파일 동기화</li><li>Azure 파일 동기화 및 데이터 상자</li><li>Azure 파일 동기화 및 저장소 마이그레이션 서비스</li><li>Robocopy</li></ul> |
| Windows Server 2012 및 이전 버전 | <ul><li>Azure 파일 동기화 및 데이터 상자</li><li>Azure 파일 동기화 및 저장소 마이그레이션 서비스</li></ul> | <ul><li>Azure 파일 동기화 및 저장소 마이그레이션 서비스</li><li>Robocopy</li></ul> |
| 네트워크 연결 스토리지(NAS) | <ul><li>[Azure 파일 동기화 및 로보카피](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| 리눅스 또는 삼바 | <ul><li>[Azure 파일 동기화 및 로보카피](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>Robocopy</li></ul> |
| 마이크로소프트 Azure StorSimple 클라우드 어플라이언스 8100 또는 StorSimple 클라우드 어플라이언스 8600 | <ul><li>[Azure 파일 동기화 및 StorSimple 클라우드 어플라이언스 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| 스토심플 클라우드 어플라이언스 1200 | <ul><li>[Azure 파일 동기화](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>마이그레이션 도구 상자

### <a name="file-copy-tools"></a>파일 복사 도구

Microsoft 및 기타 에서 사용할 수 있는 여러 파일 복사 도구가 있습니다. 마이그레이션 시나리오에 적합한 도구를 선택하려면 다음 의 근본적인 질문을 고려해야 합니다.

* 도구가 파일 복사본의 소스 및 대상 위치를 지원합니까?

* 이 도구가 원본과 대상 저장소 위치 간에 네트워크 경로 또는 사용 가능한 프로토콜(예: REST, SMB 또는 NFS)을 지원합니까?

* 이 도구는 소스 및 대상 위치에서 지원하는 필요한 파일 충실도를 유지합니까?

    경우에 따라 대상 저장소가 원본과 동일한 충실도를 지원하지 않는 경우도 있습니다. 대상 저장소가 필요에 충분한 경우 도구는 대상의 파일 충실도 기능만 일치해야 합니다.

* 이 도구에 마이그레이션 전략에 적합한 기능이 있습니까?

    예를 들어 이 도구를 사용하여 가동 중지 시간을 최소화할 수 있는지 여부를 고려합니다.
    
    도구가 소스를 대상에 미러리칭하는 옵션을 지원하는 경우 소스에 액세스할 수 있는 상태를 유지하면서 동일한 소스 및 대상에서 여러 번 실행할 수 있습니다.

    도구를 처음 실행하면 대량의 데이터가 복사됩니다. 이 초기 실행은 잠시 동안 지속될 수 있습니다. 비즈니스 프로세스에 대해 데이터 원본을 오프라인으로 전환하는 데 필요한 시간보다 오래 지속되는 경우가 많습니다.

    **로보카피/MIR과**마찬가지로 소스를 대상에 미러링하면 동일한 소스 및 대상에서 도구를 다시 실행할 수 있습니다. 이전 실행 후에 발생하는 원본 변경 내용만 전송해야 하기 때문에 실행 속도가 훨씬 빠릅니다. 이 방법으로 복사 도구를 다시 실행하면 가동 중지 시간이 크게 줄어듭니다.

다음 표는 Microsoft 도구와 Azure 파일 공유에 대한 현재 적합성을 분류합니다.

| 권장 | 도구 | Azure 파일 공유 지원 | 파일 충실도 보존 |
| :-: | :-- | :---- | :---- |
|![예, 권장 사항](media/storage-files-migration-overview/circle-green-checkmark.png)| Robocopy | 지원됩니다. Azure 파일 공유를 네트워크 드라이브로 탑재할 수 있습니다. | 완전한 충실도.* |
|![예, 권장 사항](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 파일 동기화 | 기본적으로 Azure 파일 공유에 통합됩니다. | 완전한 충실도.* |
|![예, 권장 사항](media/storage-files-migration-overview/circle-green-checkmark.png)| 스토리지 마이그레이션 서비스 | 간접적으로 지원됩니다. Azure 파일 공유는 SMS 대상 서버의 네트워크 드라이브로 탑재할 수 있습니다. | 완전한 충실도.* |
|![완전히 권장되지 않음](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | 지원됩니다. | 메타데이터를 복사하지 않습니다. [데이터 상자는 Azure 파일 동기화](storage-sync-offline-data-transfer.md). |
|![권장되지 않음](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|![권장되지 않음](media/storage-files-migration-overview/circle-red-x.png)| Azure Storage Explorer | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|![권장되지 않음](media/storage-files-migration-overview/circle-red-x.png)| Azure 데이터 팩터리 | 지원됩니다. | 메타데이터를 복사하지 않습니다. |
|||||

*\*전체 충실도: Azure 파일 공유 기능을 충족하거나 초과합니다.*

### <a name="migration-helper-tools"></a>마이그레이션 도우미 도구

이 섹션에서는 마이그레이션을 계획하고 실행하는 데 도움이 되는 도구에 대해 설명합니다.

#### <a name="robocopy-from-microsoft-corporation"></a>마이크로소프트 주식회사로보카피

RoboCopy는 파일 마이그레이션에 가장 적합한 도구 중 하나입니다. 그것은 윈도우의 일부로 온다. 주요 [RoboCopy 설명서는이](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 도구의 많은 옵션에 대 한 유용한 리소스입니다.

#### <a name="treesize-from-jam-software-gmbh"></a>JAM 소프트웨어 GmbH에서 트리 사이즈

Azure File Sync는 주로 총 저장소 양이 아닌 항목 수(파일 및 폴더)로 확장됩니다. TreeSize 도구를 사용하면 Windows Server 볼륨의 항목 수를 확인할 수 있습니다.

이 도구를 사용하여 [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)전에 큐브 뷰를 만들 수 있습니다. 배포 후 클라우드 계층이 종사하는 경우에도 사용할 수 있습니다. 이 시나리오에서는 항목의 수와 서버 캐시를 가장 많이 사용하는 디렉터리가 표시됩니다.

이 도구의 테스트 버전은 버전 4.4.1입니다. 클라우드 계층 파일과 호환됩니다. 이 도구는 정상적인 작동 중에 계층화 된 파일을 불러 들이지 않습니다.

## <a name="next-steps"></a>다음 단계

1. Azure 파일 공유(클라우드 전용 또는 하이브리드)의 배포를 위한 계획을 만듭니다.
1. 사용 가능한 마이그레이션 가이드 목록을 검토하여 Azure 파일 공유의 원본 및 배포와 일치하는 자세한 가이드를 찾습니다.

이 문서에서 언급한 Azure Files 기술에 대한 자세한 내용은 다음과 같습니다.

* [Azure 파일 공유 개요](storage-files-introduction.md)
* [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
* [Azure 파일 동기화: 클라우드 계층화](storage-sync-cloud-tiering.md)
