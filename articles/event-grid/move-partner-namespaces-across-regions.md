---
title: Azure Event Grid 파트너 네임스페이스를 다른 지역으로 이동
description: 이 문서에서는 Azure Event Grid 파트너 네임스페이스를 한 지역에서 다른 지역으로 이동하는 방법을 설명합니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89086213"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Azure Event Grid 파트너 네임스페이스를 다른 지역으로 이동
여러 이유로 리소스를 다른 지역으로 이동하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용하거나 내부 정책 및 거버넌스 요구 사항을 충족하기 위해 이동하거나 용량 계획 요구 사항에 대한 응답으로 이동할 수 있습니다. 

이 문서에서 설명하는 대략적인 단계는 다음과 같습니다: 

- **파트너 네임스페이스** 리소스를 Azure Resource Manager 템플릿으로 내보냅니다. 템플릿의 이벤트 채널 리소스에 대한 정의를 삭제합니다. 이벤트 채널에는 고객이 소유한 파트너 토픽의 Azure Resource Manager ID에 대한 참조를 포함할 수 있습니다. 따라서 대상 지역에서 템플릿을 사용하여 만들 수 없습니다.  
- **템플릿을 사용하여 파트너 네임스페이스** 를 대상 지역에 배포합니다. 그런 다음 대상 지역의 새 파트너 네임스페이스에 이벤트 채널을 만듭니다. 
- **이동을 완료** 하려면 원본 지역에서 파트너 네임스페이스를 삭제합니다. 

    > [!NOTE]
    > - 고객이 파트너 토픽을 직접 만들 수 없기 때문에 **파트너 토픽** 을 Azure Resource Manager 템플릿으로 내보낼 수 없습니다. 
    > - **파트너 등록** 은 전체 리소스(특정 지역에 연결되지 않음)이므로 한 지역에서 다른 지역으로의 이동은 적용할 수 없습니다. 

## <a name="prerequisites"></a>필수 구성 요소
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작하려면 파트너 네임스페이스용 Resource Manager 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽의 검색 표시줄에 **Event Grid 파트너 네임스페이스** 를 입력하고 결과 목록에서 **Event Grid 파트너 네임스페이스** 를 선택합니다. 
3. Resource Manager 템플릿으로 내보내려는 **파트너 네임스페이스** 를 선택합니다. 
4. **Event Grid 파트너 네임스페이스** 페이지의 왼쪽 메뉴에서 **설정** 아래의 **템플릿 내보내기** 를 선택한 다음, 도구 모음에서 **다운로드** 를 선택합니다. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="템플릿 내보내기 -> 다운로드" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. 포털에서 다운로드한 **.zip** 파일을 찾은 다음, 선택한 폴더에서 파일의 압축을 풉니다. zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함되어 있습니다. 
1. 선택한 편집기에서 **template.json** 을 엽니다. 
8. **토픽** 리소스의 `location`을 대상 지역 또는 위치로 업데이트합니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 만들기 
템플릿을 배포하여 대상 지역에 파트너 네임스페이스를 만듭니다. 

1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
3. **템플릿 배포** 를 선택합니다.
4. **만들기** 를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.
7. **저장** 을 선택하여 템플릿을 저장합니다. 
8. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다: 
    1. Azure **구독** 을 선택합니다. 
    1. 대상 지역에서 기존 **리소스 그룹** 을 선택하거나 새로 만듭니다. 
    1. **위치** 에서 대상 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 
    1. **파트너 네임스페이스 이름** 에 새 파트너 네임스페이스의 이름을 입력합니다. 
    1. 파트너 등록 외부 ID에 `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` 형식으로 파트너 등록 리소스 ID를 입력합니다.
    1. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.     
    1. **검토 + 만들기** 를 선택하여 배포 프로세스를 시작합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토하고 **만들기** 를 선택합니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료하려면 원본 지역에서 파트너 네임스페이스를 삭제합니다.  

새로 시작하려면 대상 지역에서 파트너 네임스페이스를 삭제한 다음, 이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에 있는 단계를 반복합니다.

Azure Portal를 사용하여 파트너 네임스페이스를 삭제하려면 다음을 수행합니다:

1. Azure Portal 위쪽의 검색 창에 **Event Grid 파트너 네임스페이스** 를 입력하고 검색 결과에서 **Event Grid 파트너 네임스페이스** 를 선택합니다. 
2. 삭제할 파트너 네임스페이스를 선택하고 도구 모음에서 **삭제** 를 선택합니다. 
3. 삭제를 **확인** 하여 파트너 네임스페이스를 삭제합니다. 

## <a name="next-steps"></a>다음 단계
Event Grid 파트너 네임스페이스를 한 지역에서 다른 지역으로 이동하는 방법을 배웠습니다. 지역 간 시스템 토픽, 사용자 지정 토픽, 도메인을 이동하려면 다음 문서를 참조하세요.

- [지역 간 시스템 토픽 이동](move-system-topics-across-regions.md). 
- [지역 간 사용자 지정 토픽 이동](move-custom-topics-across-regions.md). 
- [지역 간 도메인 이동](move-domains-across-regions.md).

지역 간 리소스를 이동하는 방법과 Azure의 재해 복구에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md) 문서를 참조하세요.