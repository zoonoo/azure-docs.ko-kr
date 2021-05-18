---
title: Azure Relay 네임스페이스를 다른 지역으로 이동
description: 이 문서에서는 Azure Relay 네임스페이스를 현재 지역에서 다른 지역으로 이동하는 방법을 보여줍니다.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463831"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Azure Relay 네임스페이스를 다른 지역으로 이동
이 문서에서는 Azure Relay 네임스페이스를 하나의 지역에서 다른 지역으로 이동하는 방법을 보여줍니다. 대략적인 단계는 다음과 같습니다.

1. Relay 네임스페이스를 Azure Resource Manager 템플릿으로 **내보냅니다**.
1. 템플릿의 리소스 **위치(지역) 업데이트** 입니다. 또한 템플릿에서 모든 **동적** WCF Relay를 삭제합니다. 

    WCF Relay에는 두 가지 모드가 있습니다. 첫 번째 모드에서는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 WCF Relay를 명시적으로 만듭니다. Azure Portal의 **WCF Relay** 페이지에서, 이 모드의 릴레이에 대해 **isDynamic** 속성이 **false** 로 설정된 것을 볼 수 있습니다. 

    두 번째 모드에서는 수신기(서버)가 지정된 엔드포인트 주소에 대해 연결될 때 WCF Relay가 자동으로 생성됩니다. 수신기가 릴레이에 연결되어 있으면 Azure Portal의 WCF Relay 목록에 해당 릴레이가 표시됩니다. 이 모드에서 릴레이의 경우 **isDynamic** 속성은 동적으로 생성되기 때문에 **true** 로 설정됩니다. 수신기의 연결이 끊어지면 동적 WCF Relay가 중단됩니다. 
1. 템플릿을 사용하여 대상 지역에 리소스를 **배포합니다**.

## <a name="prerequisites"></a>필수 구성 요소
대상 지역에서 Azure Relay 서비스를 사용할 수 있는지 확인합니다. [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)을 참조하세요. 
 
## <a name="prepare"></a>준비
시작하려면 Resource Manager 템플릿을 내보냅니다. 이 템플릿에는 Azure Relay 네임스페이스를 설명하는 설정이 포함되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 후 Azure Relay 네임스페이스를 선택합니다.
3. 왼쪽 메뉴의 **설정** 에서 **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택합니다.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager 템플릿 다운로드":::
5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다. zip 파일에는 템플릿 및 매개 변수 JSON 파일이 포함되어 있습니다. 
1. 선택한 편집기의 추출된 폴더에서 **template.json** 파일을 엽니다.
1. `location`를 검색 하고, 속성 값을 해당 지역의 새 이름으로 바꿉니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.
1. **동적 WCF Relay**(유형: `Microsoft.Relay/namespaces/WcfRelays`) 리소스의 정의를 제거합니다 . 동적 WCF Relay는 **릴레이** 페이지에서 **isDynamic** 속성을 **true** 로 설정한 것입니다. 다음 예제에서 **echoservice** 는 동적 WCF Relay이며, 템플릿에서 그 정의를 제거해야 합니다. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="동적 릴레이":::

## <a name="move"></a>이동
템플릿을 배포하여 대상 지역에 릴레이 네임스페이스를 만듭니다. 

1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서, 검색 텍스트에 대한 **템플릿 배포** 를 입력하고, **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택한 다음, **입력** 키를 누릅니다.

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
1. **리소스 그룹** 페이지에서 Azure Relay 네임스페이스를 선택합니다. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Azure Relay 네임스페이스 선택":::    
1. **Azure Relay 네임스페이스** 페이지의 왼쪽 메뉴에서 **하이브리드 연결** 또는 **WCF Relay** 를 선택하여 하이브리드 연결 및 WCF Relay가 만들어졌는지 확인합니다. 템플릿을 가져오기 전에 동적 WCF Relay에 대한 정의를 삭제하지 않은 경우, **WCF Relay** 페이지에서 이를 삭제합니다. 동적 WCF Relay는 클라이언트가 릴레이 네임스페이스에 연결될 때 자동으로 만들어집니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후 다시 시작하려면 **대상 Azure Relay 네임스페이스** 를 삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복하면 됩니다.

변경 내용을 커밋하고 네임스페이스 이동을 완료하려면, 원본 지역에서 **Azure Relay 네임스페이스를** 삭제합니다. 

Azure Portal을 사용하여 Azure Relay 네임스페이스(원본 또는 대상)를 삭제하려면 다음을 수행합니다.

1. Azure Portal 맨 위에 있는 검색 창에서 **릴레이** 를 입력 하고, 검색 결과에서 **서비스** 의 **릴레이** 를 선택합니다. 목록에 모든 Azure Relay 네임스페이스가 표시됩니다.
2. 삭제할 대상 네임스페이스를 선택하여 **릴레이** 페이지를 엽니다. 
1. **릴레이** 페이지에서, 도구 모음의 **삭제** 를 선택합니다. 

    ![네임스페이스 삭제 - 단추](./media/move-across-regions/delete-namespace-button.png)
3. **네임스페이스 삭제** 페이지에서 Azure Relay 네임스페이스의 이름을 입력하여 삭제를 확인한 다음, **삭제** 를 선택합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Relay 네임스페이스를 한 지역에서 다른 지역으로 이동했습니다. Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
