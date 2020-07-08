---
title: Azure Event Hubs 네임 스페이스를 다른 지역으로 이동 | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs 네임 스페이스를 현재 지역에서 다른 지역으로 이동 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: a70397772d22a65046f87877deab6263d4b2104f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312959"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure Event Hubs 네임 스페이스를 다른 지역으로 이동
기존 Event Hubs 네임 스페이스를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트를 위한 동일한 구성으로 네임 스페이스를 만들 수 있습니다. [재해 복구 계획](event-hubs-geo-dr.md#setup-and-failover-flow)의 일부로 다른 지역에 보조 네임 스페이스를 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 기존 Event Hubs 네임 스페이스에 대 한 Azure Resource Manager 템플릿을 내보낸 다음 템플릿을 사용 하 여 다른 지역에서 동일한 구성 설정을 사용 하 여 네임 스페이스를 만드는 방법을 보여 줍니다. 그러나이 프로세스는 아직 처리 되지 않은 이벤트는 이동 하지 않습니다. 원래 네임 스페이스를 삭제 하기 전에 해당 이벤트를 처리 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 계정에서 사용하는 서비스 및 기능이 대상 지역에서 지원되는지 확인합니다.
- 미리 보기 기능의 경우 구독이 대상 지역에 대한 허용 목록에 추가되었는지 확인합니다.
- 네임 스페이스에서 event hubs에 대해 **캡처 기능** 을 사용 하도록 설정한 경우 Event Hubs 네임 스페이스를 이동 하기 전에 [Azure Storage 또는 Azure Data Lake Store gen 2](../storage/common/storage-account-move.md) 또는 [Azure Data Lake Store gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) 계정을 이동 합니다. 이 문서에서 설명 하는 것과 유사한 단계를 수행 하 여 저장소 및 Event Hubs 네임 스페이스를 포함 하는 리소스 그룹을 다른 지역으로 이동할 수도 있습니다. 
- Event Hubs 네임 스페이스가 **Event Hubs 클러스터**에 있는 경우이 문서의 단계를 진행 하기 전에 **대상 지역** 에 [전용 클러스터를 만듭니다](event-hubs-dedicated-cluster-create-portal.md) . [GitHub에서 빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) 사용 하 여 Event Hubs 클러스터를 만들 수도 있습니다. 템플릿에서 JSON의 네임 스페이스 부분을 제거 하 여 클러스터만 만듭니다. 

## <a name="prepare"></a>준비
시작 하려면 리소스 관리자 템플릿을 내보냅니다. 이 템플릿에는 Event Hubs 네임 스페이스를 설명 하는 설정이 포함 되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스** 를 선택한 다음 Event Hubs 네임 스페이스를 선택 합니다.

3. > **설정**  >  **템플릿 내보내기**를 선택 합니다.

4. **템플릿 내보내기** 페이지에서 **다운로드** 를 선택 합니다.

    ![리소스 관리자 템플릿 다운로드](./media/move-across-regions/download-template.png)

5. 포털에서 다운로드 한 .zip 파일을 찾아 원하는 폴더에 해당 파일의 압축을 풉니다.

   이 zip 파일에는 템플릿을 포함 하는 .zip 파일 및 템플릿을 배포 하는 스크립트가 포함 되어 있습니다.


## <a name="move"></a>이동

템플릿을 배포 하 여 대상 지역에 Event Hubs 네임 스페이스를 만듭니다. 


1. Azure Portal에서 **리소스 만들기**를 선택 합니다.

2. **Marketplace 검색**에서 **템플릿 배포**를 입력 하 고 **enter**키를 누릅니다.

3. **템플릿 배포**를 선택 합니다.

4. **만들기**를 선택합니다.

5. **편집기에서 고유한 템플릿 빌드**를 선택 합니다.

6. **파일 로드**를 선택 하 고 지침에 따라 마지막 섹션에서 다운로드 한 파일 **에template.js** 를 로드 합니다.

7. **저장** 을 선택 하 여 템플릿을 저장 합니다. 

8. **사용자 지정 배포** 페이지에서 다음 단계를 수행 합니다. 

    1. Azure **구독**을 선택 합니다. 

    2. 기존 **리소스 그룹**을 선택하거나 리소스 그룹을 만듭니다. 원본 네임 스페이스가 Event Hubs 클러스터에 있는 경우 대상 지역에서 클러스터가 포함 된 리소스 그룹을 선택 합니다. 

    3. 대상 **위치** 또는 지역을 선택 합니다. 기존 리소스 그룹을 선택한 경우이 설정은 읽기 전용입니다. 

    4. **설정** 섹션에서 다음 단계를 수행 합니다.
    
        1. 새 **네임 스페이스 이름을**입력 합니다. 

            ![리소스 관리자 템플릿 배포](./media/move-across-regions/deploy-template.png)

        2. 원본 네임 스페이스가 **Event Hubs 클러스터**에 있는 경우 **리소스 그룹** 의 이름을 입력 하 고 클러스터를 **외부 ID**의 일부로 **Event Hubs** 합니다. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 네임 스페이스의 이벤트 허브가 저장소 계정을 사용 하 여 이벤트를 캡처하는 경우 리소스 그룹 이름 및 저장소 계정 필드를 지정 합니다 `StorageAccounts_<original storage account name>_external` . 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. 
    
    6. 이제 **구매 선택** 을 선택 하 여 배포 프로세스를 시작 합니다. 

## <a name="discard-or-clean-up"></a>삭제 또는 정리
배포 후에는 시작 하려는 경우 **대상 Event Hubs 네임 스페이스**를 삭제 하 고이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명 된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 Event Hubs 네임 스페이스의 이동을 완료 하려면 **소스 Event Hubs 네임 스페이스**를 삭제 합니다. 네임 스페이스를 삭제 하기 전에 네임 스페이스의 모든 이벤트를 처리 했는지 확인 합니다. 

Azure Portal를 사용 하 여 Event Hubs 네임 스페이스 (원본 또는 대상)를 삭제 하려면 다음을 수행 합니다.

1. Azure Portal의 맨 위에 있는 검색 창에서 **Event Hubs**를 입력 하 고 검색 결과에서 **Event Hubs** 을 선택 합니다. 목록에 Event Hubs 네임 스페이스가 표시 됩니다.

2. 삭제할 대상 네임 스페이스를 선택 하 고 도구 모음에서 **삭제** 를 선택 합니다. 

    ![네임 스페이스 삭제-단추](./media/move-across-regions/delete-namespace-button.png)

3. **리소스 삭제*** 페이지에서 선택한 리소스를 확인 하 고 **예**를 입력 하 여 삭제를 확인 한 다음 **삭제**를 선택 합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Event Hubs 네임 스페이스를 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
