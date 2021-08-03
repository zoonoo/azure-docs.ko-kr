---
title: Service Bus 네임스페이스를 다른 지역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 네임스페이스를 현재 지역에서 다른 지역으로 이동하는 방법을 보여줍니다.
ms.topic: how-to
ms.date: 06/08/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9a752e845cf4b819e327bc922fc13199b79ab8d3
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811450"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Azure Service Bus 네임스페이스를 다른 지역으로 이동
기존 Service Bus 네임스페이스를 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 사용하여 네임스페이스를 만들 수 있습니다. [재해 복구 계획](service-bus-geo-dr.md)의 일부로 다른 지역에 보조 네임스페이스를 만들 수도 있습니다.

대략적인 단계는 다음과 같습니다.

1. 현재 지역의 Service Bus 네임스페이스를 Azure Resource Manager 템플릿으로 내보냅니다. 
1. 템플릿의 리소스 위치를 업데이트합니다. 또한 자동으로 생성되는 기본 규칙을 만들 수 없으므로 템플릿에서 기본 구독 필터를 제거합니다. 
1. 템플릿을 사용하여 Service Bus 네임스페이스를 대상 지역에 배포합니다. 
1. 배포를 확인하여 토픽에 대한 네임스페이스, 큐, 토픽 및 구독이 모두 대상 지역에 만들어졌는지 확인합니다. 
1. 모든 메시지를 처리한 후 원본 영역에서 네임스페이스를 삭제하여 이동을 완료합니다. 

## <a name="prerequisites"></a>필수 구성 요소
계정에서 사용하는 Azure Service Bus 및 기능이 대상 지역에서 지원되는지 확인합니다.
 
## <a name="prepare"></a>준비
시작하려면 Resource Manager 템플릿을 내보냅니다. 이 템플릿에는 Service Bus 네임스페이스를 설명하는 설정이 포함되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 후 Service Bus 네임스페이스를 선택합니다.
3. **Service Bus 네임스페이스** 페이지의 왼쪽 메뉴에 있는 **자동화** 에서 **템플릿 내보내기** 를 선택합니다. 
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택합니다.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager 템플릿 다운로드":::
5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다. 이 zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함되어 있습니다. 
1. 추출된 폴더에서 template.json 파일을 엽니다. 
1. `location`을 검색하고, 속성 값을 해당 지역 또는 위치의 새 이름으로 바꿉니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.
1. 다음 형식의 리소스 정의를 제거합니다: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules`. JSON을 유효하게 유지하려면 이 섹션 앞의 쉼표(`,`) 문자를 제거해야 합니다.  

    > [!NOTE]
    > Resource Manager 템플릿을 사용하여 구독에 대한 기본 규칙을 만들 수 없습니다. 기본 규칙은 대상 지역에서 구독을 만들 때 자동으로 생성됩니다. 

## <a name="move"></a>이동
템플릿을 배포하여 대상 지역에 Service Bus 네임스페이스를 만듭니다. 

1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **마켓플레이스 검색** 에서, 검색 텍스트에 대한 **템플릿 배포** 를 입력하고, **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택한 다음, **입력** 을 누릅니다.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="새 템플릿 배포":::    
1. **템플릿 배포** 페이지에서 **만들기** 를 선택합니다.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="새 템플릿 배포 - 만들기 단추":::        
1. **사용자 지정 배포** 페이지에서 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="편집기에서 사용자 고유의 템플릿 빌드 - 연결":::            
1. **템플릿 편집** 페이지에서, 도구 모음의 **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="템플릿 선택":::                
1. **저장** 을 선택하여 템플릿을 저장합니다. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="템플릿 저장":::                    
1. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다: 
    1. Azure **구독** 을 선택합니다. 
    2. 기존 **리소스 그룹** 을 선택하거나 리소스 그룹을 만듭니다. 
    3. 대상 **위치** 또는 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 
    4. 새 **네임스페이스 이름** 을 입력합니다.
    1. **검토 + 만들기** 를 선택합니다. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Resource Manager 템플릿 배포":::
    1. **검토 + 만들기** 페이지의 맨 아래에서 **만들기** 를 선택합니다. 
    
## <a name="verify"></a>확인
1. 배포가 성공하면 **리소스 그룹으로 이동** 을 선택합니다.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="리소스 그룹 연결로 이동":::    
1. **리소스 그룹 페이지** 에서 Service Bus 네임스페이스를 선택합니다. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Service Bus 네임스페이스 선택":::    
1. **Service Bus 네임스페이스** 페이지에서 원본 지역의 큐, 토픽 및 구독이 표시되는지 확인합니다. 
    1. 오른쪽 창의 맨 아래에 있는 네임스페이스에 **큐** 가 표시됩니다.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="네임스페이스의 큐":::
    2. **토픽** 탭으로 전환하여 네임스페이스에서 토픽을 확인
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="네임스페이스의 토픽":::
    3. 토픽을 선택하여 구독이 만들어졌는지 확인합니다. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="토픽 구독":::      
    
    

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후 다시 시작하려면 **대상 Service Bus 네임스페이스** 를 삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복하면 됩니다.

변경 내용을 커밋하고 Service Bus 네임스페이스 이동을 완료하려면, **Service Bus 네임스페이스 원본** 을 삭제합니다. 네임스페이스를 삭제하기 전에 모든 메시지를 처리해야 합니다. 

Azure Portal을 사용하여 Service Bus 네임스페이스(원본 또는 대상)를 삭제하려면 다음을 수행합니다:

1. Azure Portal의 상단 검색 창에 **Service Bus** 를 입력하고 검색 결과에서 **Service Bus** 를 선택합니다. 목록에 Service Bus 네임스페이스가 표시됩니다.
2. 삭제할 대상 네임스페이스를 선택하고 도구 모음에서 **삭제** 를 선택합니다. 

    ![네임스페이스 삭제 - 단추](./media/move-across-regions/delete-namespace-button.png)
3. **리소스 삭제** 페이지에서 선택한 리소스를 확인하고 **예** 를 입력하여 삭제를 확인한 다음 **삭제** 를 선택합니다. 

    다른 옵션은 Service Bus 네임스페이스가 있는 리소스 그룹을 삭제하는 것입니다. **리소스 그룹** 페이지에서 도구 모음의 **리소스 그룹 삭제** 를 선택한 다음 삭제를 확인합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Service Bus 네임스페이스를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
