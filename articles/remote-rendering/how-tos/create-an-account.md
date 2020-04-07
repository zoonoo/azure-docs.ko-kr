---
title: Azure 원격 렌더링 계정 만들기
description: Azure 원격 렌더링에 대한 계정을 만드는 단계를 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681299"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure 원격 렌더링 계정 만들기

이 장에서는 **Azure 원격 렌더링** 서비스에 대한 계정을 만드는 단계를 안내합니다. 빠른 시작 또는 자습서를 완료하기 위해 유효한 계정이 필수입니다.

## <a name="create-an-account"></a>계정 만들기

Azure 원격 렌더링 서비스에 대한 계정을 만드는 데 다음 단계가 필요합니다.

1. [혼합 현실 미리 보기 페이지로](https://aka.ms/MixedRealityPrivatePreview) 이동
1. '리소스 만들기' 버튼을 클릭합니다.
1. 검색 필드("마켓플레이스 검색")에서 '원격 렌더링'을 입력하고 'enter'를 누르고 있습니다.
1. 결과 목록에서 "원격 렌더링" 타일을 클릭합니다.
1. 다음 화면에서 "만들기" 버튼을 클릭합니다. 새 원격 렌더링 계정을 만들기 위한 양식이 열립니다.
    1. 계정 이름으로 '리소스 이름'을 설정합니다.
    1. 필요한 경우 '구독' 업데이트
    1. '리소스 그룹'을 선택한 리소스 그룹으로 설정합니다.
1. 계정이 만들어지면 계정을 탐색하고 다음을 수행합니다.
    1. *개요* 탭에서 '계정 ID'를 기록합니다.
    1. 액세스 *키 설정 >* '기본 키'를 기록합니다.

### <a name="retrieve-the-account-information"></a>계정 정보 검색

샘플 및 자습서에서는 계정 ID와 키를 제공해야 합니다. 예를 들어 PowerShell 샘플 스크립트에 사용되는 **arrconfig.json** 파일에서:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

*지역* 을 채울 [사용 가능한 지역 목록을](../reference/regions.md) 참조하십시오.

다음 단계에 **`arrAccountId`** **`arrAccountKey`** 설명된 대로 포털에서 찾을 수 있는 값입니다.

* [Azure 포털로](https://www.portal.azure.com) 이동
* **"원격 렌더링 계정"을** 찾으십시오 - **"최근 리소스"** 목록에 있어야 합니다. 상단의 검색 표시줄에서 검색할 수도 있습니다. 이 경우 사용할 구독이 기본 구독 필터(검색 모음 옆의 필터 아이콘)에서 선택되어 있는지 확인합니다.

![등록 필터](./media/azure-subscription-filter.png)

계정을 클릭하면 계정 **ID가** 바로 표시되는 이 화면으로 이동합니다.

![Azure 계정 ID](./media/azure-account-id.png)

키의 경우 왼쪽 패널에서 **액세스 키를** 선택합니다. 다음 페이지에는 기본 키와 보조 키가 표시됩니다.

![Azure 액세스 키](./media/azure-account-primary-key.png)

의 **`arrAccountKey`** 값은 기본 키또는 보조 키일 수 있습니다.

## <a name="link-storage-accounts"></a>저장소 계정 링크

이 단락은 저장소 계정을 원격 렌더링 계정에 연결하는 방법을 설명합니다. 저장소 계정이 연결되어 있는 경우 모델을 로드할 때와 같이 계정의 데이터와 상호 작용할 때마다 SAS URI를 생성할 필요가 없습니다. 대신 [모델 로드 섹션에](../concepts/models.md#loading-models)설명된 대로 저장소 계정 이름을 직접 사용할 수 있습니다.

이 단락의 단계는 이 대체 액세스 방법을 사용해야 하는 각 저장소 계정에 대해 수행해야 합니다. 아직 저장소 계정을 만들지 않은 경우 [빠른 시작을 렌더링하기 위한 모델 변환의](../quickstarts/convert-model.md#storage-account-creation)각 단계를 안내할 수 있습니다.

이제 저장소 계정이 있다고 가정합니다. 포털의 저장소 계정으로 이동하여 해당 저장소 계정의 **IAM(액세스 제어)** 탭으로 이동합니다.

![스토리지 계정 IAM](./media/azure-storage-account.png)

 역할 할당을 추가할 수 있도록 이 저장소 계정에 대한 소유자 권한이 있는지 확인합니다. 액세스 권한이 없는 경우 **역할 할당 추가** 옵션이 비활성화됩니다.

 다음 단계에서 설명한 대로 세 가지 고유한 역할을 추가해야 합니다. 세 가지 수준의 액세스를 모두 제공하지 않는 경우 저장소 계정에 액세스하는 동안 권한 문제가 있습니다.

 "역할 할당 추가" 타일에서 **추가** 단추를 클릭하여 첫 번째 역할을 추가합니다.

![스토리지 계정 IAM](./media/azure-add-role-assignment.png)

* 할당할 첫 번째 역할은 위의 스크린샷과 같이 **소유자입니다.** 
* * 드롭다운에**대한 액세스 할당에서** **원격 렌더링 계정을** 선택합니다.
* 마지막 드롭다운에서 구독 및 원격 렌더링 계정을 선택합니다.

**역할** 드롭다운에서 각 선택 항목에 대해 새 역할을 두 번 더 추가하는 것을 반복합니다.
* **Storage 계정 기여자**
* **Storage Blob 데이터 기여자**

다른 드롭다운은 첫 번째 단계에서와 같이 선택됩니다.

세 가지 역할을 모두 추가한 경우 Azure Remote Rendering 계정은 관리되는 서비스 ID를 할당된 시스템을 사용하여 저장소 계정에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [인증](authentication.md)
* [인증을 위해 Azure 프런트 엔드 API 사용](frontend-apis.md)
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
