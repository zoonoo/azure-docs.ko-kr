---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971359"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **+리소스 만들기** > **미디어** > **Media Services** 를 클릭합니다.
1. **미디어 서비스 계정 만들기** 섹션에서 필요한 값을 입력합니다.

    | Name | Description |
    | ---|---|
    |**계정 이름**|새 Media Services 계정의 이름을 입력합니다. Media Services 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.|
    |**구독**|둘 이상의 구독이 있는 경우 액세스 권한이 있는 Azure 구독 목록에서 하나를 선택합니다.|
    |**리소스 그룹**|새 리소스 또는 기존 리소스를 선택합니다. 리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../../../azure-resource-manager/management/overview.md#resource-groups)를 참조하세요.|
    |**위치**|Media Services 계정에 대한 미디어 및 메타데이터 레코드를 저장하는 데 사용할 지리적 지역을 선택합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다. 사용 가능한 Media Services 지역만 드롭다운 목록 상자에 표시됩니다. |
    |**Storage 계정**|Media Services 계정에서 미디어 콘텐츠의 Blob Storage를 제공할 스토리지 계정을 선택합니다. Media Services 계정과 동일한 지역의 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만들 수 있습니다. 동일한 지역에 새 스토리지 계정이 생성됩니다. 스토리지 계정 이름에 대한 규칙은 Media Services 계정의 경우와 같습니다.<br/><br/>**기본** 스토리지 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 스토리지 계정은 여러 개 사용할 수 있습니다. Azure Portal을 사용하여 보조 스토리지 계정을 추가할 수 있습니다. 자세한 내용은 [Azure Media Services 계정이 있는 Azure Storage 계정](../storage-account-concept.md)을 참조하세요.<br/><br/>Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 있는 스토리지 계정을 사용하는 것이 좋습니다.|
    |**고급 설정**| **시스템 관리** 라디오 단추를 선택하여 시스템 관리 ID를 통해 계정을 만들 수 있습니다.  이렇게 선택하면 고객 관리형 키를 사용하거나 자체 키(BYOK) 및 Media Services를 가져와 신뢰할 수 있는 스토리지를 사용하도록 설정할 수 있습니다.  고객 관리형 키에 대한 자세한 내용은 [Media Services를 사용하여 자체 키(고객 관리형 키) 가져오기](../concept-use-customer-managed-keys-byok.md)를 참조하세요. 또한 [관리 ID](../concept-managed-identities.md)도 사용하도록 설정됩니다.

1. 계정 배포 진행 상태를 보려면 **대시보드에 고정** 을 선택합니다.
1. 양식 맨 아래에 있는 **만들기** 를 클릭합니다.
