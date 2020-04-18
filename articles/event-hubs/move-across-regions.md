---
title: Azure 이벤트 허브 네임스페이스를 다른 리전으로 이동 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Event Hubs 네임스페이스를 현재 리전에서 다른 리전으로 이동하는 방법을 보여 주며 있습니다.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606809"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure 이벤트 허브 네임스페이스를 다른 지역으로 이동
기존 이벤트 허브 네임스페이스를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 가진 네임스페이스를 만들 수 있습니다. [재해 복구 계획의](event-hubs-geo-dr.md#setup-and-failover-flow)일부로 다른 지역에서 보조 네임스페이스를 만들 수도 있습니다.

> [!NOTE]
> 이 문서에서는 기존 Event Hubs 네임스페이스에 대한 Azure Resource Manager 템플릿을 내보낸 다음 템플릿을 사용하여 다른 지역의 동일한 구성 설정으로 네임스페이스를 만드는 방법을 보여 주며 이 문서에서는 다음과 같은 네임스페이스를 만듭니다. 그러나 이 프로세스는 아직 처리되지 않은 이벤트를 이동하지 않습니다. 이벤트를 삭제하기 전에 원래 네임스페이스에서 이벤트를 처리해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 계정에서 사용하는 서비스 및 기능이 대상 리전에서 지원되도록 합니다.
- 미리 보기 기능의 경우 구독이 대상 지역에 대해 제외되어 있는지 확인합니다.
- 네임스페이스에서 이벤트 허브에 대해 **캡처 기능을** 사용하도록 설정한 경우 이벤트 허브 네임스페이스를 이동하기 전에 [Azure 저장소 또는 Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) 또는 [Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) 계정을 이동합니다. 이 문서에서 설명하는 것과 유사한 단계를 수행하여 저장소 및 이벤트 허브 네임스페이스를 모두 포함하는 리소스 그룹을 다른 지역으로 이동할 수도 있습니다. 
- 이벤트 허브 네임스페이스가 **이벤트 허브 클러스터에**있는 경우 이 문서의 단계를 진행하기 전에 대상 **지역에** 전용 [클러스터를 만듭니다.](event-hubs-dedicated-cluster-create-portal.md) 

## <a name="prepare"></a>준비
시작하려면 리소스 관리자 템플릿을 내보냅니다. 이 템플릿에는 이벤트 허브 네임스페이스를 설명하는 설정이 포함되어 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 리소스를** 선택한 다음 이벤트 허브 네임스페이스를 선택합니다.

3. > **설정** > 내보내기 템플릿 을**선택합니다.**

4. **내보내기 템플릿** 페이지에서 **다운로드를** 선택합니다.

    ![리소스 관리자 템플릿 다운로드](./media/move-across-regions/download-template.png)

5. 포털에서 다운로드한 .zip 파일을 찾아 해당 파일의 압축을 선택하려는 폴더로 압축을 해제합니다.

   이 zip 파일에는 템플릿을 배포하는 템플릿 및 스크립트가 포함된 .json 파일이 포함되어 있습니다.


## <a name="move"></a>이동

템플릿을 배포하여 대상 지역에서 이벤트 허브 네임스페이스를 만듭니다. 


1. Azure 포털에서 **리소스 만들기를 선택합니다.**

2. **Marketplace 검색**에서 **템플릿 배포**를 입력하고 **ENTER**를 누릅니다.

3. **템플릿 배포**를 선택합니다.

4. **만들기**를 선택합니다.

5. **편집기에서 사용자 고유의 템플릿을 빌드합니다.** 를 선택합니다.

6. **파일 로드를**선택한 다음 지침에 따라 마지막 섹션에서 다운로드한 **template.json** 파일을 로드합니다.

7. 템플릿을 저장하려면 **저장을** 선택합니다. 

8. 사용자 **지정 배포** 페이지에서 다음 단계를 따릅니다. 

    1. Azure **구독을**선택합니다. 

    2. 기존 **리소스 그룹**을 선택하거나 리소스 그룹을 만듭니다. 소스 네임스페이스가 이벤트 허브 클러스터에 있는 경우 대상 지역에 클러스터가 포함된 리소스 그룹을 선택합니다. 

    3. 대상 **위치** 또는 지역을 선택합니다. 기존 리소스 그룹을 선택한 경우 이 설정은 읽기 전용입니다. 

    4. **설정** 섹션에서 다음 단계를 수행합니다.
    
        1. 새 **네임스페이스 이름을**입력합니다. 

            ![리소스 관리자 템플릿 배포](./media/move-across-regions/deploy-template.png)

        2. 소스 네임스페이스가 **이벤트 허브 클러스터에**있는 경우 **외부 ID의**일부로 **리소스 그룹** 및 이벤트 **허브 클러스터의** 이름을 입력합니다. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 네임스페이스의 이벤트 허브가 이벤트를 캡처하기 위해 Storage 계정을 사용하는 경우 `StorageAccounts_<original storage account name>_external` 리소스 그룹 이름과 필드에 대한 저장소 계정을 지정합니다. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. 
    
    6. 이제 **구매 선택을** 선택하여 배포 프로세스를 시작합니다. 

## <a name="discard-or-clean-up"></a>폐기 또는 정리
배포 후 다시 시작하려는 경우 대상 이벤트 **허브 네임스페이스를**삭제하고 이 문서의 [준비](#prepare) 및 [이동](#move) 섹션에 설명된 단계를 반복할 수 있습니다.

변경 내용을 커밋하고 이벤트 허브 네임스페이스의 이동을 완료하려면 **소스 이벤트 허브 네임스페이스를 삭제합니다.** 네임스페이스를 삭제하기 전에 네임스페이스의 모든 이벤트를 처리했는지 확인합니다. 

Azure 포털을 사용하여 이벤트 허브 네임스페이스(소스 또는 대상)를 삭제하려면 다음을 수행하십시오.

1. Azure 포털 상단의 검색 창에서 **이벤트 허브를**입력하고 검색 결과에서 **이벤트 허브를 선택합니다.** 목록에 이벤트 허브 네임스페이스가 표시됩니다.

2. 삭제할 대상 네임스페이스를 선택하고 도구 모음에서 **삭제를** 선택합니다. 

    ![네임스페이스 삭제 - 버튼](./media/move-across-regions/delete-namespace-button.png)

3. 리소스 **삭제*** 페이지에서 선택한 리소스를 확인하고 **yes를**입력하여 삭제를 확인한 다음 **삭제를 선택합니다.** 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Event Hubs 네임스페이스를 한 리전에서 다른 리전으로 이동하고 소스 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
