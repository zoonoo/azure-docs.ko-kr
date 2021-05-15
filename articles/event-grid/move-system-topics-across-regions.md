---
title: Azure Event Grid 시스템 토픽을 다른 지역으로 이동
description: 이 문서에서는 Azure Event Grid 시스템 토픽을 한 지역에서 다른 지역으로 이동하는 방법을 보여줍니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89086184"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Azure Event Grid 시스템 토픽을 다른 지역으로 이동
여러 이유로 리소스를 다른 지역으로 이동하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용하거나 내부 정책 및 거버넌스 요구 사항을 충족하기 위해 이동하거나 용량 계획 요구 사항에 대한 응답으로 이동할 수 있습니다. 

이 문서에서 설명하는 대략적인 단계는 다음과 같습니다: 

- Azure Storage 계정 및 관련 시스템 토픽이 포함된 **리소스 그룹** 을 Resource Manager 템플릿으로 내보냅니다. 시스템 토픽의 템플릿만 내보낼 수도 있습니다. 이 경로로 이동하는 경우 시스템 토픽을 이동하기 전에 Azure 이벤트 원본(이 예제에서는 Azure Storage 계정)을 다른 지역으로 이동해야 합니다. 그런 다음 시스템 토픽에 대해 내보낸 템플릿에서 대상 지역의 스토리지 계정용 외부 ID를 업데이트합니다. 
- **템플릿을 수정** 하여 시스템 토픽을 구독하는 웹후크를 가리키도록 `endpointUrl` 속성을 추가합니다. 시스템 토픽을 내보낼 때 해당 구독(이 경우에는 웹후크)도 템플릿으로 내보내지만 `endpointUrl` 속성은 포함되지 않습니다. 따라서 토픽을 구독하는 엔드포인트를 가리키도록 업데이트해야 합니다. 또한 `location` 특성의 값을 새 위치 또는 지역으로 업데이트합니다. 다른 형식 이벤트 처리기의 경우 위치만 업데이트해야 합니다. 
- **템플릿을 사용하여 대상 지역에 리소스를 배포합니다**. 대상 지역에서 만들 스토리지 계정 및 시스템 토픽의 이름을 지정합니다. 
- **배포 확인**. 대상 지역의 Blob Storage에 파일을 업로드할 때 웹후크가 호출되는지 확인합니다. 
- **이동을 완료** 하려면 원본 지역에서 리소스(이벤트 소스 및 시스템 토픽)를 삭제합니다. 

## <a name="prerequisites"></a>필수 구성 요소
- 원본 지역에서 [빠른 시작: Azure Portal을 사용하여 웹 엔드포인트로 Blob Storage 이벤트 라우팅](blob-event-quickstart-portal.md)을 완료합니다. 이 단계는 **선택 사항** 입니다. 이 문서의 단계를 테스트합니다. App Service 및 App Service 요금제의 별도 리소스 그룹에 스토리지 계정을 유지합니다. 
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작하려면 시스템 이벤트 원본(Azure Storage 계정) 및 관련 시스템 토픽이 포함된 리소스 그룹용 Resource Manager 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다. 그런 다음 시스템 토픽이 생성된 이벤트 원본이 포함된 리소스 그룹을 선택합니다. 다음 예제는 **Azure Storage** 계정입니다. 리소스 그룹에는 스토리지 계정 및 관련 시스템 토픽이 포함됩니다. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="리소스 그룹 페이지":::        
3. 왼쪽 메뉴의 **설정** 에서 **템플릿 내보내기** 를 선택한 다음 도구 모음에서 **다운로드** 를 선택합니다. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="스토리지 계정 - 템플릿 내보내기 페이지":::        
5. 포털에서 다운로드한 **.zip** 파일을 찾은 다음, 선택한 폴더에서 파일의 압축을 풉니다. zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함되어 있습니다. 
1. 선택한 편집기에서 **template.json** 을 엽니다. 
1. 웹후크 URL은 템플릿으로 내보내지 않습니다. 또한 다음 단계를 수행합니다:
    1. 템플릿 파일에서 **웹후크** 를 검색합니다. 
    1. **속성** 섹션에서 마지막 줄의 끝에 쉼표(`,`) 문자를 추가합니다. 이 예에서는 `"preferredBatchSizeInKilobytes": 64`입니다. 
    1. 다음 예제와 같이 웹후크 URL에 값이 설정된 `endpointUrl` 속성을 추가합니다. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > 다른 형식 이벤트 처리기의 경우 모든 속성을 템플릿으로 내보냅니다. 다음 단계에 표시된 대로 `location` 속성을 대상 지역으로 업데이트하기만 하면 됩니다. 
7. **스토리지 계정** 리소스에 대한 `location`을 대상 지역 또는 위치로 업데이트합니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. 템플릿의 **시스템 토픽** 리소스에 대해 `location`을 업데이트하려면 단계를 반복합니다. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 만들기 
템플릿을 배포하여 대상 지역의 스토리지 계정에 대한 스토리지 계정 및 시스템 토픽을 만듭니다. 

1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
3. **템플릿 배포** 를 선택합니다.
4. **만들기** 를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.
7. **저장** 을 선택하여 템플릿을 저장합니다. 
8. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다. 
    1. Azure **구독** 을 선택합니다. 
    1. 대상 지역에서 기존 **리소스 그룹** 을 선택하거나 새로 만듭니다. 
    1. **지역** 에서 대상 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다.
    1. **시스템 토픽 이름** 의 경우 스토리지 계정과 연결될 시스템 토픽의 이름을 입력합니다.  
    1. **스토리지 계정 이름** 에는 대상 지역에 만들 스토리지 계정의 이름을 입력합니다. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Resource Manager 템플릿 배포":::
    5. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토하고 **만들기** 를 선택합니다. 

## <a name="verify"></a>확인
1. 배포에 성공하면 **리소스 그룹으로 이동** 을 선택합니다. 
1. **리소스 그룹** 페이지에서 이벤트 소스(이 예제에서는 Azure Storage 계정) 및 시스템 토픽이 만들어졌는지 확인합니다. 
1. Azure Blob Storage의 컨테이너에 파일을 업로드하고 웹후크가 이벤트를 수신했는지 확인합니다. 자세한 내용은 [엔드포인트에 이벤트 보내기](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)를 참조하세요.

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료하려면 원본 지역에서 스토리지 계정 및 관련 시스템 토픽이 포함된 리소스 그룹을 삭제합니다.  

새로 시작하려면 대상 지역에서 리소스 그룹을 삭제한 다음, 이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에 있는 단계를 반복합니다.

Azure Portal을 사용하여 리소스 그룹(원본 또는 대상)을 삭제하려면:

1. Azure Portal 맨 위에 있는 검색 창에 **리소스 그룹** 을 입력하고 검색 결과에서 **리소스 그룹** 을 선택합니다. 
2. 삭제할 리소스 그룹을 선택한 다음, 도구 모음에서 **삭제** 를 선택합니다. 

    리소스 그룹 삭제
3. 확인 페이지에서 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.  

## <a name="next-steps"></a>다음 단계
Azure 이벤트 원본 및 연결된 시스템 토픽을 한 지역에서 다른 지역으로 이동하는 방법을 배웠습니다. 지역 간에 사용자 정의 토픽, 도메인 및 파트너 네임스페이스를 이동하려면 다음 문서를 참조하세요.

- [지역 간 사용자 지정 토픽 이동](move-custom-topics-across-regions.md). 
- [지역 간 도메인 이동](move-domains-across-regions.md). 
- [지역 간 파트너 네임스페이스 이동](move-partner-namespaces-across-regions.md). 

지역 간 리소스를 이동하는 방법과 Azure의 재해 복구에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md) 문서를 참조하세요.
