---
title: Azure Remote Rendering 계정 만들기
description: Azure Remote Rendering을 위한 계정을 만드는 단계 설명
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 7a4e6d80d80441a1b94c1fb2bd8f82f247235fe3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318095"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure Remote Rendering 계정 만들기

이 장에서는 **Azure Remote Rendering** 서비스에 대한 계정을 만드는 단계를 안내합니다. 빠른 시작 또는 자습서를 완료하려면 유효한 계정이 필수입니다.

## <a name="create-an-account"></a>계정 만들기

Azure Remote Rendering 서비스에 대한 계정을 만들려면 다음 단계가 필요합니다.

1. [Mixed Reality 미리 보기 페이지](https://aka.ms/MixedRealityPrivatePreview)로 이동
1. ‘리소스 만들기’ 단추 클릭
1. 검색 필드(“마켓플레이스 검색”)에 "Remote Rendering"을 입력하고 ‘Enter’를 누릅니다.
1. 결과 목록에서 "Remote Rendering" 타일 클릭
1. 다음 화면에서 “만들기” 단추를 클릭합니다. 새 Remote Rendering 계정을 만들기 위한 양식이 열립니다.
    1. '리소스 이름'을 계정 이름으로 설정
    1. 필요한 경우 ‘구독’ 업데이트
    1. ' 리소스 그룹 '을 원하는 리소스 그룹으로 설정
    1. 이 리소스를 만들어야 하는 ' 위치 ' 드롭다운에서 지역을 선택 합니다. 아래 [계정 영역](create-an-account.md#account-regions) 에 대 한 설명을 참조 하세요.
1. 계정을 만든 후 이동하고
    1. *개요* 탭에서 '계정 ID' 확인
    1. *설정 > 액세스 키* 탭에서 '기본 키' 확인. 계정의 비밀 계정 키임

### <a name="account-regions"></a>계정 지역
계정 생성 시 지정 된 위치에 따라 계정 리소스가 할당 된 지역이 결정 됩니다. 이를 만든 후에는 변경할 수 없습니다. 그러나 계정 위치에 관계 없이 지원 되는 모든 [지역](./../reference/regions.md)에서 원격 렌더링 세션에 연결 하는 데 계정을 사용할 수 있습니다.

### <a name="retrieve-the-account-information"></a>계정 정보 검색

샘플과 자습서를 사용하려면 계정 ID와 키를 제공해야 합니다. 예를 들어 PowerShell 샘플 스크립트에 사용되는 **arrconfig.json** 파일에서

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

*지역* 옵션을 입력하는 데 [사용 가능한 지역](../reference/regions.md) 목록을 참조하세요.

다음 단계에서 설명하는 것처럼 **`arrAccountId`** 및 **`arrAccountKey`** 값을 포털에서 찾을 수 있습니다.

* [Azure Portal](https://www.portal.azure.com)로 이동
* **"Remote Rendering 계정"** 을 찾습니다. **"최근 리소스"** 목록에 있을 것입니다. 위쪽의 검색 창에서 검색할 수도 있습니다. 이 경우 사용하려는 구독이 기본 구독 필터에서 선택되어 있는지 확인합니다(검색 창 옆 필터 아이콘).

![구독 필터](./media/azure-subscription-filter.png)

계정을 클릭하면 이 화면이 나타나고 **계정 ID**가 바로 표시됩니다.

![Azure 계정 ID](./media/azure-account-id.png)

왼쪽 패널에서 키에 대한 **액세스 키**를 선택합니다. 다음 페이지는 기본 및 보조 키를 표시합니다.

![Azure 액세스 키](./media/azure-account-primary-key.png)

**`arrAccountKey`** 값은 기본 키 또는 보조 키일 수 있습니다.

## <a name="link-storage-accounts"></a>스토리지 계정 연결

이 단락에서는 스토리지 계정을 Remote Rendering 계정에 연결하는 방법을 설명 합니다. 스토리지 계정이 연결되면 모델 로드 등, 계정의 데이터와 상호 작용할 때마다 매번 SAS URI를 생성할 필요가 없습니다. 대신 [모델 로드 섹션](../concepts/models.md#loading-models)에서 설명한 대로 스토리지 계정 이름을 직접 사용할 수 있습니다.

이 단락의 단계는 이 대체 액세스 방법을 사용하는 스토리지 계정 각각에 대해 수행해야 합니다. 스토리지 계정을 아직 만들지 않은 경우 [렌더링을 위한 모델 변환 빠른 시작](../quickstarts/convert-model.md#storage-account-creation)의 해당 단계를 통해 살펴볼 수 있습니다.

여기서는 스토리지 계정이 있다고 가정합니다. 포털의 스토리지 계정으로 이동하고 해당 스토리지 계정에 대한 **IAM(Access Control)** 탭으로 이동합니다.

![스토리지 계정 IAM](./media/azure-storage-account.png)

 역할 할당을 추가할 수 있게 이 스토리지 계정에 대한 소유자 권한이 있는지 확인합니다. 액세스 권한이 없으면 **역할 할당 추가** 옵션이 비활성화됩니다.

 다음 단계에서 설명하는 것처럼 세 가지 고유 역할을 추가해야 합니다. 3개 액세스 수준을 일부 제공하지 않는 경우 스토리지 계정에 액세스를 시도할 때 권한 문제가 발생하게 됩니다.

 "역할 할당 추가" 타일에서 **추가** 단추를 클릭하여 첫 번째 역할을 추가합니다.

![저장소 계정 IAM 역할 할당 추가](./media/azure-add-role-assignment.png)

* 할당할 첫 번째 역할은 위의 스크린샷에 표시된 것처럼 **소유자**입니다.
* **액세스 할당** 드롭다운에서 **Remote Rendering 계정**을 선택합니다.
* 마지막 드롭다운에서 구독 및 Remote Rendering 계정을 선택합니다.

> [!WARNING]
> Remote Rendering 계정이 나열되지 않는 경우 이 [문제 해결 섹션](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account)을 참조하세요.

**역할** 드롭다운에서 각 선택 항목에 대해 새 역할을 두 번 반복하여 추가합니다.

* **Storage 계정 기여자**
* **Storage Blob 데이터 기여자**

첫 번째 단계에서 다른 드롭다운이 선택됩니다.

세 역할을 모두 추가했으면 Azure Remote Rendering 계정이 시스템에서 할당한 관리 서비스 ID를 사용하여 스토리지 계정에 액세스할 수 있습니다.
> [!IMPORTANT]
> Azure 역할 할당은 Azure Storage에 의해 캐시 되므로 원격 렌더링 계정에 대 한 액세스 권한을 부여 하는 경우와 저장소 계정에 액세스 하는 데 사용할 수 있는 시간 사이에 최대 30 분의 지연이 발생할 수 있습니다. 자세한 내용은 [역할 기반 액세스 제어 설명서](https://docs.microsoft.com/azure/role-based-access-control/troubleshooting#role-assignment-changes-are-not-being-detected) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [인증](authentication.md)
* [인증을 위해 Azure Frontend API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
