---
title: Azure 파일 공유로 마이그레이션
description: Azure 파일 공유로의 마이그레이션에 대해 알아보고 마이그레이션 가이드를 확인합니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a18421163e39fbcf5c1081c79cf06d982613fafb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769121"
---
# <a name="migrate-to-azure-file-shares"></a>Azure 파일 공유로 마이그레이션

이 문서에서는 Azure 파일 공유로 마이그레이션하는 기본적인 사항을 설명합니다.

이 문서에는 마이그레이션 기본 사항 및 마이그레이션 가이드 표가 포함되어 있습니다. 이러한 가이드는 파일을 Azure 파일 공유로 이동할 때 유용합니다. 이 가이드는 데이터가 있는 위치 및 이동 하려는 배포 모델(클라우드 전용 또는 하이브리드)을 기반으로 구성됩니다.

## <a name="migration-basics"></a>마이그레이션 기본 사항

Azure에서는 여러 클라우드 스토리지 유형을 사용할 수 있습니다. Azure에 대한 파일 마이그레이션의 기본은 데이터에 가장 적합한 Azure 스토리지 옵션을 결정하는 것입니다.

[Azure 파일 공유](storage-files-introduction.md)는 범용 파일 데이터에 적합합니다. 이 데이터에는 온-프레미스 SMB 또는 NFS 공유를 사용할 수 있는 모든 항목이 포함됩니다. [Azure 파일 동기화](../file-sync/file-sync-planning.md)로 Windows Server를 온-프레미스로 실행하는 서버에 여러 Azure 파일 공유의 콘텐츠를 캐시할 수 있습니다.

현재 온-프레미스 서버에서 실행되는 앱의 경우 Azure 파일 공유에 파일을 저장하는 것이 나을 수 있습니다. 앱을 Azure로 이동하고 Azure 파일 공유를 공유 스토리지로 사용할 수 있습니다. 이 시나리오에 [Azure Disks](../../virtual-machines/managed-disks-overview.md)를 고려할 수도 있습니다.

일부 클라우드 앱은 SMB 또는 컴퓨터 로컬 데이터 액세스 또는 공유 액세스를 사용하지 않습니다. 이러한 앱의 경우에는 [Azure Blob](../blobs/storage-blobs-overview.md) 같은 개체 스토리지를 선택하는 것이 가장 적합한 경우가 많습니다.

마이그레이션의 핵심은 파일을 현재 스토리지 위치에서 Azure로 이동할 때 적용 가능한 모든 파일 충실도를 캡처하는 것입니다. Azure 스토리지 옵션이 지원하는 충실도 수준과, 시나리오에 필요한 수준은 적합한 Azure 스토리지를 고르는 데도 도움이 됩니다. 일반적으로 범용 파일 데이터는 파일 메타데이터에 따라 다릅니다. 앱 데이터는 그렇지 않을 수 있습니다.

파일의 두 가지 기본 구성 요소는 다음과 같습니다.

- **데이터 스트림**: 파일의 데이터 스트림은 파일 콘텐츠를 저장합니다.
- **파일 메타 데이터**: 파일 메타데이터에는 다음과 같은 하위 구성 요소가 있습니다.
   * 읽기 전용 같은 파일 특성
   * *NTFS 권한* 또는 *파일 및 폴더 ACL* 이라고 하는 파일 사용 권한
   * 타임스탬프, 무엇보다 생성 및 최종 수정 타임스탬프
   * 대체 데이터 스트림. 더 많은 크기의 비표준 속성을 저장하기 위한 공간

마이그레이션의 파일 충실도는 다음과 같은 기능으로 정의할 수 있습니다.

- 원본에 적용 가능한 모든 파일 정보를 저장합니다.
- 마이그레이션 도구를 통해 파일을 전송합니다.
- 마이그레이션의 대상 스토리지에 파일을 저장합니다. </br> 궁극적으로 이 페이지의 마이그레이션 가이드 대상은 하나 이상의 Azure 파일 공유입니다. 이 [Azure 파일 공유가 지원하지 않는 기능 / 파일 충실도 목록](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)을 고려합니다.

원활한 마이그레이션 진행을 위해 [요구 사항에 가장 적합한 복사 도구](#migration-toolbox)를 확인하고 원본에 스토리지 대상을 일치시킵니다.

앞의 정보를 고려하여, Azure에서 범용 파일의 대상 스토리지가 [Azure 파일 공유](storage-files-introduction.md)임을 확인할 수 있습니다.

Azure Blob의 개체 스토리지와는 달리 Azure 파일 공유는 기본적으로 파일 메타데이터를 저장할 수 있습니다. 또한 Azure 파일 공유는 파일 및 폴더 계층 구조, 특성 및 권한을 유지합니다. NTFS 권한은 온-프레미스에 있기 때문에 파일 및 폴더에 저장할 수 있습니다.

온-프레미스 도메인 컨트롤러인 Active Directory 사용자는 기본적으로 Azure 파일 공유에 액세스할 수 있습니다. Azure AD DS(Azure Active Directory Domain Services)의 사용자도 그렇습니다. 각각 자신의 현재 ID를 사용하여 공유 권한과, 파일 및 폴더 ACL에 따라 액세스 권한을 얻습니다. 이 동작은 사용자가 온-프레미스 파일 공유에 연결하는 것과 유사합니다.

대체 데이터 스트림은 현재 Azure 파일 공유의 파일에 저장할 수 없는 파일 충실도의 주요 측면입니다. Azure 파일 동기화를 사용할 경우 온-프레미스에 유지됩니다.

Azure 파일 공유를 위한 [온-프레미스 Active Directory 인증](storage-files-identity-auth-active-directory-enable.md) 및 [Azure AD DS 인증](storage-files-identity-auth-active-directory-domain-service-enable.md)에 대해 자세히 알아봅니다.

## <a name="migration-guides"></a>마이그레이션 가이드

다음 표는 자세한 마이그레이션 가이드 목록입니다.

표 사용 방법:

1. 파일이 현재 저장된 원본 시스템에 대한 행을 찾습니다.

1. 다음 대상 중 하나를 선택합니다.

   - Azure 파일 동기화를 사용하여 Azure 파일 공유의 콘텐츠를 온-프레미스에 캐시하는 하이브리드 배포
   - 클라우드의 Azure 파일 공유

   선택한 항목과 일치하는 대상 열을 선택합니다.

1. 원본 및 대상이 교차하는 범위에서 표 셀이 사용 가능한 마이그레이션 시나리오를 나열합니다. 자세히 마이그레이션 가이드에 직접 연결하려면 하나를 선택합니다.

링크가 없는 시나리오에는 아직 게시된 마이그레이션 가이드가 없습니다. 이 표에서 수시로 업데이트를 확인하세요. 새 가이드가 제공되면 게시됩니다.

| 원본 | 대상: </br>하이브리드 배포 | 대상: </br>클라우드 전용 배포 |
|:---|:--|:--|
| | 도구 조합:| 도구 조합: |
| Windows Server 2012 R2 이상 | <ul><li>[Azure 파일 동기화](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure 파일 동기화 및 Azure DataBox](storage-files-migration-server-hybrid-databox.md)</li></ul> | <ul><li>[RoboCopy를 통해 탑재된 Azure 파일 공유](storage-files-migration-robocopy.md)</li><li>Azure 파일 동기화로</li></ul> |
| Windows Server 2012 및 이전 버전 | <ul><li>DataBox와 최근 서버 OS에 대한 Azure 파일 동기화로</li><li>Azure 파일 동기화를 사용한 최근 서버에 스토리지 마이그레이션 서비스를 사용한 다음 업로드</li></ul> | <ul><li>Azure 파일 동기화를 사용한 최근 서버에 대한 스토리지 마이그레이션 서비스로</li><li>[RoboCopy를 통해 탑재된 Azure 파일 공유](storage-files-migration-robocopy.md)</li></ul> |
| NAS(네트워크 연결 스토리지) | <ul><li>[Azure 파일 동기화 업로드로](storage-files-migration-nas-hybrid.md)</li><li>[DataBox + Azure 파일 동기화로](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[DataBox로](storage-files-migration-nas-cloud-databox.md)</li><li>[RoboCopy를 통해 탑재된 Azure 파일 공유](storage-files-migration-robocopy.md)</li></ul> |
| Linux/Samba | <ul><li>[Azure 파일 동기화 및 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>[RoboCopy를 통해 탑재된 Azure 파일 공유](storage-files-migration-robocopy.md)</li></ul> |
| Microsoft Azure StorSimple 8100 또는 8600 시리즈 어플라이언스 | <ul><li>[전용 데이터 마이그레이션 클라우드 서비스로](storage-files-migration-storsimple-8000.md)</li></ul> | <ul><li>[전용 데이터 마이그레이션 클라우드 서비스로](storage-files-migration-storsimple-8000.md)</li></ul> |
| StorSimple 1200 가상 어플라이언스 | <ul><li>[Azure 파일 동기화로](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>마이그레이션 도구 상자

### <a name="file-copy-tools"></a>파일 복사 도구

Microsoft 및 기타에서 사용할 수 있는 몇 가지 파일 복사 도구가 있습니다. 마이그레이션 시나리오에 적합한 도구를 선택하려면 다음과 같은 기본적인 질문을 고려해야 합니다.

* 도구가 파일 복사를 위한 원본 및 대상 위치를 지원하는가?

* 도구가 원본 및 대상 스토리지 위치 사이에서 네트워크 경로나 사용 가능한 프로토콜(예: REST, SMB 또는 NFS)을 지원하는가?

* 도구가 원본 및 대상 위치에서 지원하는 필요한 파일 충실도를 유지하는가?

    경우에 따라 대상 스토리지는 원본과 동일한 충실도를 지원하지 않습니다. 대상 스토리지가 요구에 부합한다면 도구는 대상의 파일 충실도 기능과만 일치해야 합니다.

* 도구에 마이그레이션 전략에 맞는 기능이 있는가?

    예를 들어 도구를 사용하여 가동 중지 시간을 최소화할 수 있는지 고려합니다.
    
    도구에서 소스를 대상에 미러링하는 옵션을 지원하는 경우 소스에 액세스할 수 있는 동안 동일한 소스 및 대상에서 여러 번 실행할 수 있습니다.

    도구를 처음 실행하면 대량의 데이터를 복사합니다. 이러한 최초 실행은 다소 시간이 걸릴 수 있습니다. 비즈니스 프로세스에 대한 데이터 원본을 오프라인으로 전환하는데 걸리는 시간이 생각보다 길어지는 경우가 종종 있습니다.

    **robocopy/MIR** 과 마찬가지로 소스를 대상에 미러링하면 동일한 소스 및 대상에서 도구를 다시 실행할 수 있습니다. 이전 실행 이후의 원본 변경만 전송하면 되기 때문에 이번 실행이 훨씬 빠릅니다. 이런 식으로 복사 도구를 다시 실행하면 가동 중지 시간을 크게 줄일 수 있습니다.

다음 표에서는 Microsoft 도구와 Azure 파일 공유에 대한 최신 적합성을 분류하고 있습니다.

| 권장 | 도구 | Azure 파일 공유 지원 | 파일 충실도 유지 |
| :-: | :-- | :---- | :---- |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 지원됨. Azure 파일 공유를 네트워크 드라이브로 탑재할 수 있습니다. | 완전한 충실도* |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 파일 동기화 | 기본적으로 Azure 파일 공유에 통합됩니다. | 완전한 충실도* |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| 스토리지 마이그레이션 서비스 | 간접적으로 지원됩니다. Azure 파일 공유를 SMS 대상 서버에 네트워크 드라이브로 탑재할 수 있습니다. | 완전한 충실도* |
|![예, 권장](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | 지원됨. | DataBox는 메타데이터를 완벽 지원합니다. |
|![일부 권장되지 않음](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| AzCopy </br>최신 버전 | 지원되지만 완전히 권장되지는 않습니다. | 대규모 차등 복사본을 지원하지 않으며 일부 파일 충실도가 손실될 수 있습니다. </br>[Azure 파일 공유에 AzCopy를 사용하는 방법 알아보기](../common/storage-use-azcopy-files.md) |
|![일부 권장되지 않음](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer </br>최신 버전 | 지원되나 이 옵션은 사용하지 않는 것이 좋습니다. | ACL과 같은 대부분의 파일 충실도를 잃게 됩니다. 타임스탬프를 지원합니다. |
|![권장하지 않음](media/storage-files-migration-overview/circle-red-x.png)| Azure 데이터 팩터리 | 지원됨. | 메타데이터를 복사하지 않습니다. |
|||||

*\* 전체 충실도: Azure 파일 공유 기능을 충족하거나 초과합니다.*

### <a name="migration-helper-tools"></a>마이그레이션 도우미 도구

이 섹션에서는 마이그레이션을 계획 및 실행하는 데 도움이 되는 도구에 대해 설명합니다.

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation의 RoboCopy

RoboCopy는 파일 마이그레이션에 가장 적합한 도구 중 하나입니다. Windows의 일부로 제공됩니다. 기본 [RoboCopy 설명서](/windows-server/administration/windows-commands/robocopy)는 이 도구의 여러 옵션에 대한 유용한 리소스입니다.

#### <a name="treesize-from-jam-software-gmbh"></a>JAM Software GmbH의 TreeSize

Azure 파일 동기화는 주로 전체 스토리지 크기가 아닌 항목(파일 및 폴더) 수를 사용하여 스토리지 용량을 조정합니다. TreeSize 도구를 사용하여 Windows Server 볼륨의 항목 수를 결정할 수 있습니다.

이 도구를 사용하여 [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)전에 큐브 뷰를 만들 수 있습니다. 배포 후 클라우드 계층화가 사용되는 경우에도 사용할 수 있습니다. 이 시나리오에서는 항목 수와 서버 캐시를 가장 많이 사용하는 디렉터리를 확인할 수 있습니다.

이 도구의 테스트된 버전은 버전 4.4.1입니다. 클라우드 계층 파일과 호환됩니다. 이 도구는 일반적인 작업 중 계층화된 파일의 회수를 초래하지 않습니다.

## <a name="next-steps"></a>다음 단계

1. 원하는 Azure 파일 공유(클라우드 전용 또는 하이브리드) 배포에 대한 계획을 만듭니다.
1. 사용 가능한 마이그레이션 가이드를 검토하여 Azure 파일 공유의 원본 및 배포에 부합하는 상세 가이드를 확인하세요.

Azure Files 기술에 대한 자세한 내용은 다음 문서에서 제공합니다.

* [Azure 파일 공유 개요](storage-files-introduction.md)
* [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md)
* [Azure 파일 동기화: 클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md)