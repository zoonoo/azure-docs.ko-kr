---
title: Azure Event Hubs 네임스페이스를 다른 지역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs 네임스페이스를 현재 지역에서 다른 지역으로 이동하는 방법을 보여줍니다.
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 92267ee0486823a9d6f241bb10934ca2e9aceacc
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288976"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure Event Hubs 네임스페이스를 다른 지역으로 이동
이 문서에서는 기존 Event Hubs 네임스페이스용 Azure Resource Manager 템플릿을 내보낸 다음, 템플릿을 사용하여 다른 지역에서 동일한 구성 설정으로 네임스페이스를 만드는 방법을 보여줍니다. 그러나 이 프로세스에서는 아직 처리되지 않은 이벤트는 이동하지 않습니다. 원래 네임스페이스를 삭제하기 전에 해당 이벤트를 처리해야 합니다.
 
Event Hubs 네임스페이스를 포함하는 Azure 리소스 그룹에 다른 리소스가 있는 경우 모든 관련 리소스를 한 번에 새 지역으로 이동할 수 있도록 리소스 그룹 수준에서 템플릿을 내보낼 수 있습니다. 이 문서의 단계에서는 **네임스페이스** 를 템플릿으로 내보내는 방법을 보여줍니다. **리소스 그룹** 을 템플릿으로 내보내는 단계도 비슷합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 계정에서 사용하는 서비스 및 기능이 대상 지역에서 지원되는지 확인합니다.
- 네임스페이스의 Event Hubs에 대해 **캡처 기능** 을 사용하도록 설정한 경우 Event Hubs 네임스페이스를 이동하기 전에 [Azure Storage 또는 Azure Data Lake Store gen 2](../storage/common/storage-account-move.md)나 [Azure Data Lake Store gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) 계정을 이동합니다. 이 문서에서 설명하는 단계와 유사한 단계를 수행하여 Storage 및 Event Hubs 네임스페이스를 모두 포함하는 리소스 그룹을 다른 지역으로 이동할 수도 있습니다. 
- Event Hubs 네임스페이스가 **Event Hubs 클러스터** 에 있는 경우 이 문서의 단계를 진행하기 전에 [전용 클러스터](move-cluster-across-regions.md)를 **대상 지역** 으로 이동합니다. [GitHub에서 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-cluster-namespace-eventhub/)을 사용하여 Event Hubs 클러스터를 만들 수도 있습니다. 템플릿에서 JSON의 네임스페이스 부분을 제거하여 클러스터만 만듭니다. 

## <a name="prepare"></a>준비
시작하려면 Resource Manager 템플릿을 내보냅니다. 이 템플릿에는 Event Hubs 네임스페이스를 설명하는 설정이 포함되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 리소스** 를 선택한 후 Event Hubs 네임스페이스를 선택합니다.
3. **Event Hubs 네임스페이스** 페이지의 왼쪽 메뉴에 있는 **자동화** 에서 **템플릿 내보내기** 를 선택합니다. 
4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택합니다.

    ![Resource Manager 템플릿 다운로드](./media/move-across-regions/download-template.png)
5. 포털에서 다운로드한 .zip 파일을 찾은 다음, 선택한 폴더에 파일 압축을 풉니다.

   zip 파일에는 템플릿과 템플릿 배포 스크립트를 포함하는 .json 파일이 들어 있습니다.


## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에 Event Hub 네임스페이스를 만듭니다. 


1. Azure Portal에서 **리소스 생성** 를 선택합니다.
2. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택합니다.
5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.
6. **파일 로드** 를 선택한 다음, 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.
1. 새 지역을 가리키도록 `location` 속성 값을 업데이트합니다. 위치 코드를 확인하려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역 코드는 공백이 없는 지역 이름입니다. 예를 들어 `West US`는 `westus`와 같습니다.
1. **저장** 을 선택하여 템플릿을 사용합니다. 
1. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다: 
    1. Azure **구독** 을 선택합니다. 
    2. 기존 **리소스 그룹** 을 선택하거나 리소스 그룹을 만듭니다. 원본 네임스페이스가 Event Hubs 클러스터에 있는 경우 대상 지역에서 클러스터가 포함된 리소스 그룹을 선택합니다. 
    3. 대상 **위치** 또는 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 
    4. **설정** 섹션에서 다음 단계를 수행합니다.    
        1. 새 **네임스페이스 이름** 을 입력합니다. 

            ![Resource Manager 템플릿 배포](./media/move-across-regions/deploy-template.png)
        2. 원본 네임스페이스가 **Event Hubs 클러스터** 에 있는 경우 **외부 ID** 의 일부로 **리소스 그룹** 및 **Event Hubs 클러스터** 의 이름을 입력합니다. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 네임스페이스의 이벤트 허브가 Storage 계정을 사용하여 이벤트를 캡처하는 경우 `StorageAccounts_<original storage account name>_external` 필드에 리소스 그룹 이름 및 스토리지 계정을 지정합니다. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 
    1. **검토 + 만들기** 페이지에서 설정을 검토하고 **만들기** 를 선택합니다.   

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후 다시 시작하려면 **대상 Event Hubs 네임스페이스** 를 삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복하면 됩니다.

변경 내용을 커밋하고 Event Hubs 네임스페이스 이동을 완료하려면 원래 지역에서 **Event Hubs 네임스페이스** 를 삭제합니다. 네임스페이스를 삭제하기 전에 네임스페이스의 모든 이벤트를 처리했는지 확인합니다. 

Azure Portal을 사용하여 Event Hubs 네임스페이스(원본 또는 대상)를 삭제하려면 다음을 수행합니다.

1. Azure Portal의 상단 검색 창에 **Event Hubs** 를 입력하고 검색 결과에서 **Event Hubs** 를 선택합니다. 목록에 Event Hubs 네임스페이스가 표시됩니다.
2. 삭제할 대상 네임스페이스를 선택하고 도구 모음에서 **삭제** 를 선택합니다. 

    ![네임스페이스 삭제 - 단추](./media/move-across-regions/delete-namespace-button.png)
3. **네임스페이스 삭제** 페이지에서 **네임스페이스 이름** 을 입력하여 삭제를 확인한 다음, **삭제** 를 선택합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Event Hubs 네임스페이스를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)
