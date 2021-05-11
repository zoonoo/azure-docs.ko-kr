---
title: Azure Event Hubs 전용 클러스터를 다른 지역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs 전용 클러스터를 현재 지역에서 다른 지역으로 이동하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89380834"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Azure Event Hubs 전용 클러스터를 다른 지역으로 이동
이 문서에서는 기존 Event Hubs 전용 클러스터용 Azure Resource Manager 템플릿을 내보낸 다음, 템플릿을 사용하여 다른 지역에서 동일한 구성 설정으로 클러스터를 만드는 방법을 보여 줍니다. 

Event Hubs 클러스터가 포함된 Azure 리소스 그룹에 네임스페이스 및 이벤트 허브와 같은 다른 리소스가 있는 경우 한 단계로 모든 관련 리소스를 새 지역으로 이동할 수 있도록 리소스 그룹 수준에서 템플릿을 내보내는 것이 좋습니다. 이 문서의 단계에서는 **Event Hubs 클러스터** 를 템플릿으로 내보내는 방법을 보여 줍니다. **리소스 그룹** 을 템플릿으로 내보내는 단계도 비슷합니다. 

## <a name="prerequisites"></a>사전 요구 사항
대상 지역에서 전용 클러스터를 만들 수 있는지 확인합니다. 가장 쉬운 확인 방법은 Azure Portal를 사용하여 [Event Hubs 전용 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md) 것입니다. 클러스터를 만드는 시점에 지원되는 지역 목록이 표시됩니다. 

## <a name="prepare"></a>준비
시작하려면 Resource Manager 템플릿을 내보냅니다. 이 템플릿에는 Event Hubs 전용 클러스터를 설명하는 설정이 포함되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 다음, Event Hubs 전용 클러스터를 선택합니다.
3. **설정** > **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택합니다.

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Resource Manager 템플릿 다운로드" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다.

   zip 파일에는 템플릿과 템플릿 배포 스크립트를 포함하는 .json 파일이 들어 있습니다.


## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에서 Event Hubs 전용 클러스터를 만듭니다. 


1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.
1. 새 지역을 가리키도록 `location` 속성 값을 업데이트합니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.
1. **저장** 을 선택하여 템플릿을 사용합니다. 
1. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다. 
    1. Azure **구독** 을 선택합니다. 
    2. 기존 **리소스 그룹** 을 선택하거나 리소스 그룹을 만듭니다. 
    3. 대상 **위치** 또는 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 
    4. **설정** 섹션에서 다음 단계를 수행합니다.    
        1. 새 **클러스터 이름** 을 입력합니다. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Resource Manager 템플릿 배포":::
    5. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토하고 **만들기** 를 선택합니다.  

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후에 처음부터 새로 시작하려면 **대상 Event Hubs 전용 클러스터** 를 삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 Event Hubs 클러스터 이동을 완료하려면 원래 지역에서 **Event Hubs 클러스터** 를 삭제합니다. 

Azure Portal을 사용하여 Event Hubs 클러스터(원본 또는 대상)를 삭제하려면

1. Azure Portal 맨 위에 있는 검색 창에 **Event Hubs 클러스터** 를 입력하고 검색 결과에서 **Event Hubs 클러스터** 를 선택합니다. 목록에 Event Hubs 클러스터가 표시됩니다.
2. 삭제할 클러스터를 선택한 다음, 도구 모음에서 **삭제** 를 선택합니다. 
3. **클러스터 삭제** 페이지에서 **클러스터 이름** 을 입력하여 삭제를 확인하고 **삭제** 를 선택합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Event Hubs 전용 클러스터를 한 지역에서 다른 지역으로 이동하는 방법을 배웠습니다. 

네임스페이스를 한 지역에서 다른 지역으로 이동하는 방법에 대한 지침은 [지역 간에 Event Hubs 네임스페이스 이동](move-across-regions.md) 문서를 참조하세요. 

Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)
