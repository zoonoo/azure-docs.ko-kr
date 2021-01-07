---
title: 사용자 지정 항목 Azure Event Grid 다른 지역으로 이동
description: 이 문서에서는 한 지역에서 다른 지역으로 Azure Event Grid 사용자 지정 항목을 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145345"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>사용자 지정 항목 Azure Event Grid 다른 지역으로 이동
여러 가지 이유로 리소스를 다른 지역으로 이동 하는 것이 좋습니다. 예를 들어 새 Azure 지역을 활용 하 여 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나 용량 계획 요구 사항에 대 한 응답으로 사용할 수 있습니다. 

이 문서에서 다루는 개략적인 단계는 다음과 같습니다. 

- Azure Resource Manager 템플릿으로 **사용자 지정 토픽** 리소스를 내보냅니다. 

    > [!IMPORTANT]
    > 사용자 지정 토픽만 템플릿으로 내보내집니다. 항목에 대 한 모든 구독은 내보내지 않습니다.
- **템플릿을 사용 하** 여 대상 영역에 사용자 지정 항목을 배포 합니다. 
- 대상 지역에서 **수동으로 구독을 만듭니다** . 사용자 지정 항목을 현재 지역의 템플릿에 내보낸 경우 토픽만 내보내집니다. 구독은 템플릿에 포함 되지 않으므로 대상 지역에서 사용자 지정 토픽을 만든 후 수동으로 만듭니다. 
- **배포 확인**. 사용자 지정 항목이 대상 지역에 만들어졌는지 확인 합니다. 
- **이동을 완료**하려면 원본 영역에서 사용자 지정 항목을 삭제 합니다. 

## <a name="prerequisites"></a>필수 구성 요소
- 빠른 시작: 소스 영역에서 [웹 끝점으로 사용자 지정 이벤트 라우팅을](custom-event-quickstart-portal.md) 완료 합니다. 이 단계를 수행 하 여이 문서의 단계를 테스트할 수 있습니다. 
- 대상 지역에서 Event Grid 서비스를 사용할 수 있는지 확인 합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)을 참조하세요.

## <a name="prepare"></a>준비
시작 하려면 사용자 지정 항목에 대 한 리소스 관리자 템플릿을 내보냅니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 창에서 **Event Grid 항목**을 입력 하 고 결과 목록에서 **항목 Event Grid** 를 선택 합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Event Grid 항목 검색 및 선택":::
3. 리소스 관리자 템플릿으로 내보내려는 **항목** 을 선택 합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Event Grid 항목 검색 및 선택":::   
4. **Event Grid 항목** 페이지에서 왼쪽 메뉴의 **설정** 아래에서 **템플릿 내보내기** 를 선택한 다음 도구 모음에서 **다운로드** 를 선택 합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Event Grid 항목 검색 및 선택"
    ```
1. 템플릿을 **저장** 합니다. 

## <a name="recreate"></a>다시 
템플릿을 배포 하 여 대상 지역에 사용자 지정 항목을 만듭니다. 

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
2. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.
3. **템플릿 배포**를 선택 합니다.
4. **만들기**를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드**를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.
7. **저장** 을 선택 하 여 템플릿을 저장 합니다. 
8. **사용자 지정 배포** 페이지에서 다음 단계를 수행 합니다. 
    1. Azure **구독**을 선택 합니다. 
    1. 대상 지역에서 기존 **리소스 그룹** 을 선택 하거나 하나를 만듭니다. 
    1. **지역**에서 대상 지역을 선택 합니다. 기존 리소스 그룹을 선택한 경우이 설정은 읽기 전용입니다. 
    1. **항목 이름**에 항목의 새 이름을 입력 합니다. 
    1. 페이지 아래쪽에서 **검토 + 만들기**를 선택합니다. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Event Grid 항목 검색 및 선택":::
    1. **검토 + 만들기** 페이지에서 설정을 검토 하 고 **만들기**를 선택 합니다. 

## <a name="verify"></a>확인

1. 배포에 성공한 후 **리소스로 이동**을 선택 합니다. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Event Grid 항목 검색 및 선택":::
1. 사용자 지정 항목에 대 한 **Event Grid 항목** 페이지가 표시 되는지 확인 합니다.   
1. [웹 끝점에 대 한 사용자 지정 이벤트 라우팅](custom-event-quickstart-portal.md#send-an-event-to-your-topic) 의 단계에 따라 항목에 이벤트를 보냅니다. Webhook 이벤트 처리기가 호출 되는지 확인 합니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
이동을 완료 하려면 소스 영역에서 사용자 지정 항목을 삭제 합니다.  

다시 시작 하려면 대상 지역에서 항목을 삭제 하 고이 문서의 [준비](#prepare) 및 [다시 만들기](#recreate) 섹션에서 단계를 반복 합니다.

Azure Portal를 사용 하 여 사용자 지정 토픽을 삭제 하려면 다음을 수행 합니다.

1. Azure Portal 맨 위에 있는 검색 창에서 **Event Grid 항목**을 입력 하 고 검색 결과에서 **항목 Event Grid** 을 선택 합니다. 
2. 삭제할 항목을 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.  

Azure Portal를 사용 하 여 사용자 지정 항목이 포함 된 리소스 그룹을 삭제 하려면 다음을 수행 합니다.

1. Azure Portal의 맨 위에 있는 검색 창에서 **리소스 그룹**을 입력 하 고 검색 결과에서 **리소스 그룹** 을 선택 합니다. 
2. 삭제할 리소스 그룹을 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 
3. 확인 페이지에서 리소스 그룹의 이름을 입력 하 고 **삭제**를 선택 합니다.  

## <a name="next-steps"></a>다음 단계
Event Grid 사용자 지정 항목을 한 지역에서 다른 지역으로 이동 하는 방법을 배웠습니다. 지역에서 시스템 항목, 도메인 및 파트너 네임 스페이스를 이동 하는 다음 문서를 참조 하세요.

- [여러 지역에서 시스템 항목을 이동](move-system-topics-across-regions.md)합니다. 
- [도메인을 지역 간에 이동](move-domains-across-regions.md)합니다. 
- [여러 지역에서 파트너 네임 스페이스를 이동](move-partner-namespaces-across-regions.md)합니다.

Azure에서 지역과 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)문서를 참조 하세요.