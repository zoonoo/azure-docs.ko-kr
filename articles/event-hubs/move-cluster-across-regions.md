---
title: Azure Event Hubs 전용 클러스터를 다른 지역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs 전용 클러스터를 현재 지역에서 다른 지역으로 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380834"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Azure Event Hubs 전용 클러스터를 다른 지역으로 이동
이 문서에서는 기존 Event Hubs 전용 클러스터에 대 한 Azure Resource Manager 템플릿을 내보낸 다음 템플릿을 사용 하 여 다른 지역의 동일한 구성 설정을 사용 하 여 클러스터를 만드는 방법을 보여 줍니다. 

Event Hubs 클러스터를 포함 하는 Azure 리소스 그룹에 네임 스페이스 및 event hubs와 같은 다른 리소스가 있는 경우 모든 관련 된 리소스를 한 번에 새 지역으로 이동할 수 있도록 리소스 그룹 수준에서 템플릿을 내보낼 수 있습니다. 이 문서의 단계에서는 **Event Hubs 클러스터** 를 템플릿으로 내보내는 방법을 보여 줍니다. **리소스 그룹** 을 템플릿으로 내보내는 단계는 비슷합니다. 

## <a name="prerequisites"></a>전제 조건
대상 지역에서 전용 클러스터를 만들 수 있는지 확인 합니다. 이를 확인 하는 가장 쉬운 방법은 Azure Portal를 사용 하 여 [Event Hubs 전용 클러스터를 만드는](event-hubs-dedicated-cluster-create-portal.md)것입니다. 클러스터를 만드는 시점에 지원 되는 지역 목록이 표시 됩니다. 

## <a name="prepare"></a>준비
시작 하려면 리소스 관리자 템플릿을 내보냅니다. 이 템플릿에는 Event Hubs 전용 클러스터를 설명 하는 설정이 포함 되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 다음 Event Hubs 전용 클러스터를 선택 합니다.
3. > **설정**  >  **템플릿 내보내기**를 선택 합니다.
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택 합니다.

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="리소스 관리자 템플릿 다운로드" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. 포털에서 다운로드 한 .zip 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다.

   이 zip 파일에는 템플릿을 포함 하는 .zip 파일 및 템플릿을 배포 하는 스크립트가 포함 되어 있습니다.


## <a name="move"></a>이동

템플릿을 배포 하 여 대상 지역에 Event Hubs 전용 클러스터를 만듭니다. 


1. Azure Portal에서 **리소스 만들기**를 선택 합니다.
2. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **템플릿 배포 (사용자 지정 템플릿을 사용 하 여 배포)** 을 선택 합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드**를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.
1. `location`새 영역을 가리키도록 속성의 값을 업데이트 합니다. 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백을 사용 하지 않는 지역 이름입니다 (예: `West US` ) `westus` .
1. **저장** 을 선택 하 여 템플릿을 저장 합니다. 
1. **사용자 지정 배포** 페이지에서 다음 단계를 수행 합니다. 
    1. Azure **구독**을 선택 합니다. 
    2. 기존 **리소스 그룹**을 선택하거나 리소스 그룹을 만듭니다. 
    3. 대상 **위치** 또는 지역을 선택 합니다. 기존 리소스 그룹을 선택한 경우이 설정은 읽기 전용입니다. 
    4. **설정** 섹션에서 다음 단계를 수행 합니다.    
        1. 새 **클러스터 이름을**입력 합니다. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="리소스 관리자 템플릿 배포":::
    5. 페이지 아래쪽에서 **검토 + 만들기**를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토 한 다음 **만들기**를 선택 합니다.  

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후에는 시작 하려는 경우 **대상 Event Hubs 전용 클러스터**를 삭제 하 고이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명 된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 Event Hubs 클러스터의 이동을 완료 하려면 원래 지역에서 **Event Hubs 클러스터** 를 삭제 합니다. 

Azure Portal를 사용 하 여 Event Hubs 클러스터 (원본 또는 대상)를 삭제 하려면 다음을 수행 합니다.

1. Azure Portal 맨 위에 있는 검색 창에서 **Event Hubs 클러스터**를 입력 하 고 검색 결과에서 **클러스터 Event Hubs** 를 선택 합니다. 목록에 Event Hubs 클러스터가 표시 됩니다.
2. 삭제할 클러스터를 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 
3. **클러스터 삭제** 페이지에서 **클러스터 이름을**입력 하 여 삭제를 확인 하 고 **삭제**를 선택 합니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Event Hubs 전용 클러스터를 한 지역에서 다른 지역으로 이동 하는 방법을 배웠습니다. 

한 지역에서 다른 지역으로 네임 스페이스를 이동 하는 방법에 대 한 지침은 [지역 간 Event Hubs 네임 스페이스 이동](move-across-regions.md) 문서를 참조 하세요. 

Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)
