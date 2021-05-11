---
title: Azure Event Grid 사용자 지정 토픽을 다른 지역으로 이동
description: 이 문서에서는 Azure Event Grid 사용자 지정 토픽을 한 지역에서 다른 지역으로 이동하는 방법을 보여 줍니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89145345"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Azure Event Grid 사용자 지정 토픽을 다른 지역으로 이동
여러 이유로 리소스를 다른 지역으로 이동하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용하거나 내부 정책 및 거버넌스 요구 사항을 충족하기 위해 이동하거나 용량 계획 요구 사항에 대한 응답으로 이동할 수 있습니다. 

이 문서에서 설명하는 개략적인 단계는 다음과 같습니다. 

- **사용자 지정 토픽 리소스를 Azure Resource Manager 템플릿으로 내보냅니다**. 

    > [!IMPORTANT]
    > 사용자 지정 토픽만 템플릿으로 내보내집니다. 토픽 구독은 내보내지지 않습니다.
- **템플릿을 사용하여 대상 지역에 사용자 지정 토픽을 배포합니다**. 
- **대상 지역에서 수동으로 구독을 만듭니다**. 현재 지역에서 사용자 지정 토픽을 템플릿에 내보낸 경우 토픽만 내보내집니다. 구독은 템플릿에 포함되지 않으므로 사용자 지정 토픽이 대상 지역에 생성된 후에 수동으로 만듭니다. 
- **배포 확인**. 사용자 지정 토픽이 대상 지역에 생성되었는지 확인합니다. 
- **이동을 완료** 하려면 원본 지역에서 사용자 지정 토픽을 삭제합니다. 

## <a name="prerequisites"></a>필수 구성 요소
- 원본 지역에서 [빠른 시작: 사용자 지정 이벤트를 웹 엔드포인트로 라우팅](custom-event-quickstart-portal.md)을 완료합니다. 이 단계를 수행하면 이 문서의 단계를 테스트할 수 있습니다. 
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작하려면 사용자 지정 토픽용 Resource Manager 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 창에 **Event Grid 토픽** 을 입력하고 결과 목록에서 **Event Grid 토픽** 을 선택합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Event Grid 토픽 검색 및 선택":::
3. Resource Manager 템플릿으로 내보내려는 **토픽** 을 선택합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="사용자 지정 토픽 선택":::   
4. **Event Grid 토픽** 페이지의 왼쪽 메뉴에서 **설정** 아래의 **템플릿 내보내기** 를 선택한 다음, 도구 모음에서 **다운로드** 를 선택합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="템플릿 내보내기 -> 다운로드":::   

    > [!IMPORTANT]
    > 토픽만 템플릿으로 내보내집니다. 토픽 구독은 내보내지지 않습니다. 따라서 토픽을 대상 지역으로 이동한 후에 토픽 구독을 만들어야 합니다. 
5. 포털에서 다운로드한 **.zip** 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다. zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함되어 있습니다. 
1. 선택한 텍스트 편집기에서 **template.json** 을 엽니다. 
8. **토픽** 리소스의 `location`을 대상 지역 또는 위치로 업데이트합니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 만들기 
템플릿을 배포하여 대상 지역에 사용자 지정 토픽을 만듭니다. 

1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
3. **템플릿 배포** 를 선택합니다.
4. **만들기** 를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.
7. **저장** 을 선택하여 템플릿을 사용합니다. 
8. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다. 
    1. Azure **구독** 을 선택합니다. 
    1. 대상 지역에서 기존 **리소스 그룹** 을 선택하거나 새로 만듭니다. 
    1. **지역** 에서 대상 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 
    1. **토픽 이름** 에 토픽의 새 이름을 입력합니다. 
    1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="사용자 지정 배포":::
    1. **검토 + 만들기** 페이지에서 설정을 검토하고 **만들기** 를 선택합니다. 

## <a name="verify"></a>확인

1. 배포에 성공하면 **리소스로 이동** 을 선택합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="리소스로 이동":::
1. 사용자 지정 토픽의 **Event Grid 토픽** 페이지가 표시되는지 확인합니다.   
1. [사용자 지정 이벤트를 웹 엔드포인트로 라우팅](custom-event-quickstart-portal.md#send-an-event-to-your-topic)의 단계에 따라 토픽에 이벤트를 보냅니다. 웹후크 이벤트 처리기가 호출되었는지 확인합니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료하려면 원본 지역에서 사용자 지정 토픽을 삭제합니다.  

새로 시작하려면 대상 지역에서 토픽을 삭제한 다음, 이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에 있는 단계를 반복합니다.

Azure Portal을 사용하여 사용자 지정 토픽을 삭제하려면 다음을 수행합니다.

1. Azure Portal 맨 위에 있는 검색 창에 **Event Grid 토픽** 을 입력하고 검색 결과에서 **Event Grid 토픽** 을 선택합니다. 
2. 삭제할 토픽을 선택한 다음, 도구 모음에서 **삭제** 를 선택합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.  

Azure Portal을 사용하여 사용자 지정 토픽이 포함된 리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal 맨 위에 있는 검색 창에 **리소스 그룹** 을 입력하고 검색 결과에서 **리소스 그룹** 을 선택합니다. 
2. 삭제할 리소스 그룹을 선택한 다음, 도구 모음에서 **삭제** 를 선택합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.  

## <a name="next-steps"></a>다음 단계
Event Grid 사용자 지정 토픽을 한 지역에서 다른 지역으로 이동하는 방법을 배웠습니다. 지역 간에 시스템 토픽, 도메인, 파트너 네임스페이스를 이동하려면 다음 문서를 참조하세요.

- [지역 간에 시스템 토픽 이동](move-system-topics-across-regions.md) 
- [지역 간에 도메인 이동](move-domains-across-regions.md) 
- [지역 간에 파트너 네임스페이스 이동](move-partner-namespaces-across-regions.md)

지역 간에 리소스를 이동하는 방법과 Azure의 재해 복구에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md) 문서를 참조하세요.