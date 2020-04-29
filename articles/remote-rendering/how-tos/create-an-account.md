---
title: Azure Remote Rendering 계정 만들기
description: Azure 원격 렌더링을 위한 계정을 만드는 단계에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681299"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure Remote Rendering 계정 만들기

이 장에서는 **Azure 원격 렌더링** 서비스에 대 한 계정을 만드는 단계를 안내 합니다. 유효한 계정은 빠른 시작 또는 자습서를 완료 하는 데 필수입니다.

## <a name="create-an-account"></a>계정 만들기

Azure 원격 렌더링 서비스에 대 한 계정을 만들려면 다음 단계를 수행 해야 합니다.

1. [Mixed Reality Preview 페이지로](https://aka.ms/MixedRealityPrivatePreview) 이동
1. ' 리소스 만들기 ' 단추를 클릭 합니다.
1. 검색 필드 ("marketplace 검색")에서 "원격 렌더링"을 입력 하 고 ' enter '를 누릅니다.
1. 결과 목록에서 "원격 렌더링" 타일을 클릭 합니다.
1. 다음 화면에서 "만들기" 단추를 클릭 합니다. 새 원격 렌더링 계정을 만들기 위해 양식이 열립니다.
    1. ' 리소스 이름 '을 계정 이름으로 설정 합니다.
    1. 필요한 경우 ' 구독 ' 업데이트
    1. ' 리소스 그룹 '을 원하는 리소스 그룹으로 설정 합니다.
1. 계정을 만든 후으로 이동 하 고 다음을 수행 합니다.
    1. *개요* 탭에서 ' 계정 ID '를 확인 합니다.
    1. *설정 > 액세스 키* 탭에서 ' 기본 키 '를 확인 합니다 .이는 계정의 비밀 계정 키입니다.

### <a name="retrieve-the-account-information"></a>계정 정보를 검색 합니다.

샘플과 자습서를 수행 하려면 계정 ID와 키를 제공 해야 합니다. PowerShell 샘플 스크립트에 사용 되는 **arrconfig** 파일의 경우:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

*지역* 옵션을 입력 하는 데 [사용할 수 있는 지역 목록을](../reference/regions.md) 참조 하세요.

및 **`arrAccountId`** **`arrAccountKey`** 의 값은 다음 단계에 설명 된 대로 포털에서 찾을 수 있습니다.

* [Azure Portal](https://www.portal.azure.com)로 이동
* " **원격 렌더링 계정"** 을 찾습니다. **"최근 리소스"** 목록에 있어야 합니다. 위쪽의 검색 표시줄에서 검색할 수도 있습니다. 이 경우 사용 하려는 구독이 기본 구독 필터 (검색 표시줄 옆의 필터 아이콘)에서 선택 되었는지 확인 합니다.

![등록 필터](./media/azure-subscription-filter.png)

계정을 클릭 하면 **계정 ID가** 바로 표시 되는이 화면이 표시 됩니다.

![Azure 계정 ID](./media/azure-account-id.png)

키의 왼쪽 패널에서 **액세스 키** 를 선택 합니다. 다음 페이지에는 기본 키와 보조 키가 표시 됩니다.

![Azure 액세스 키](./media/azure-account-primary-key.png)

에 대 한 **`arrAccountKey`** 값은 기본 키 또는 보조 키 중 하나일 수 있습니다.

## <a name="link-storage-accounts"></a>저장소 계정 연결

이 단락에서는 원격 렌더링 계정에 저장소 계정을 연결 하는 방법을 설명 합니다. 저장소 계정이 연결 된 경우 모델을 로드 하는 경우와 같이 계정의 데이터와 상호 작용 하려고 할 때마다 SAS URI를 생성할 필요가 없습니다. 대신 [모델 로드 섹션](../concepts/models.md#loading-models)에 설명 된 대로 저장소 계정 이름을 직접 사용할 수 있습니다.

이 단락의 단계는이 대체 액세스 방법을 사용 해야 하는 각 저장소 계정에 대해 수행 되어야 합니다. 저장소 계정을 아직 만들지 않은 경우 [렌더링을 위한 모델 변환 빠른](../quickstarts/convert-model.md#storage-account-creation)시작의 각 단계를 진행할 수 있습니다.

이제 저장소 계정이 있다고 가정 합니다. 포털에서 저장소 계정으로 이동 하 고 해당 저장소 계정에 대 한 **Access Control (IAM)** 탭으로 이동 합니다.

![저장소 계정 IAM](./media/azure-storage-account.png)

 이 저장소 계정에 대 한 소유자 권한이 있는지 확인 하 여 역할 할당을 추가할 수 있는지 확인 합니다. 액세스 권한이 없는 경우 **역할 할당 추가** 옵션을 사용할 수 없습니다.

 다음 단계에서 설명 하는 것 처럼 세 가지 고유한 역할을 추가 해야 합니다. 세 가지 액세스 수준을 모두 제공 하지 않으면 저장소 계정에 액세스 하는 동안 사용 권한 문제가 발생 하 게 됩니다.

 "역할 할당 추가" 타일에서 **추가** 단추를 클릭 하 여 첫 번째 역할을 추가 합니다.

![저장소 계정 IAM](./media/azure-add-role-assignment.png)

* 할당할 첫 번째 역할은 위의 스크린샷에 표시 된 것 처럼 **소유자** 입니다. 
* ***에 대 한 액세스 할당** 드롭다운에서 **원격 렌더링 계정을** 선택 합니다.
* 마지막 드롭다운에서 구독 및 원격 렌더링 계정을 선택 합니다.

**역할** 드롭다운에서 각 선택 항목에 대해 새 역할을 두 번 더 추가 하는 작업을 반복 합니다.
* **Storage 계정 기여자**
* **Storage Blob 데이터 기여자**

첫 번째 단계에서 다른 드롭다운이 선택 됩니다.

세 역할을 모두 추가한 경우 Azure 원격 렌더링 계정은 시스템 할당 관리 서비스 Id를 사용 하 여 저장소 계정에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [인증](authentication.md)
* [인증에 Azure 프런트 엔드 Api 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
