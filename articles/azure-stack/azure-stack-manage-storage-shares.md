---
title: Azure Stack의 storage 용량 관리 | Microsoft Docs
description: 모니터링 하 고 Azure Stack에 대 한 사용 가능한 저장소 공간을 관리 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 96145906d40e465d2427a8100b3ad9333eec3f29
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249098"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Azure Stack에 대 한 저장소 용량 관리 

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 문서의 정보를 통해 Azure Stack 클라우드 연산자 모니터링할 수 있습니다 하 고 Azure Stack 배포의 저장소 용량을 관리 합니다. Azure Stack 저장소 인프라에 사용 되는 Azure Stack 배포의 총 저장소 용량의 하위 집합을 할당 **저장소 서비스**합니다. 저장소 서비스를 배포의 노드에 해당 하는 볼륨에서 공유에 테 넌 트의 데이터를 저장 합니다.

클라우드 운영자로 서 제한 된 용량의 저장소를 사용 해야 합니다. 저장소의 크기는 구현 솔루션에 의해 정의 됩니다. 솔루션은 Azure Stack Development Kit 설치 하는 하드웨어 또는 다중 노드 솔루션을 사용 하는 경우에 OEM 공급 업체에서 제공 됩니다.

Azure Stack 저장소 용량 확장을 지원 하지 않으므로, 반드시 [모니터](#monitor-shares) 효율적인 작업을 위해 사용 가능한 저장소에 유지 됩니다.  

공유의 남은 사용 가능한 용량 제한 되 면 계획 [공간을 관리](#manage-available-space) 용량 부족을 공유 하지 못하도록 합니다.

용량을 관리 하는 옵션은 다음과 같습니다.
- 용량을 회수
- 컨테이너 마이그레이션

공유 경우 100% 사용, 저장소 서비스 더 이상 해당 공유에 대 한 함수입니다. 공유에 대 한 작업을 복원 하는 데 도움이 가져오려면 Microsoft 지원에 문의 합니다.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>볼륨 및 공유, 컨테이너 및 디스크를 이해 합니다.
### <a name="volumes-and-shares"></a>볼륨 및 공유
합니다 *저장소 서비스* 데이터가 테 넌 트에 할당 되는 별도 이름과 같은 볼륨에 사용 가능한 저장소를 분할 합니다. Azure Stack 배포에서 노드의 수와 같은 볼륨 수가 같습니다.

- 4 노드 배포의 경우에 4 개의 볼륨 나와 있습니다. 각 볼륨에 단일 공유 합니다. 다중 노드 배포에서 노드를 제거 하거나 작동 하지 않는 경우 공유 수가 감소 되지 됩니다.
- Azure Stack 개발 키트를 사용 하는 경우에 단일 공유를 사용 하 여 단일 볼륨.

저장소 서비스 공유 저장소 서비스 에서만 단독으로 사용 되므로 있습니다 해야 직접적 수정, 추가 또는 공유에 파일을 제거 합니다. 저장소 서비스에만 이러한 볼륨에 저장 된 파일에서 작동 해야 합니다.

공유 볼륨에 테 넌 트 데이터를 보관합니다. 테 넌 트 데이터 페이지 blob을 포함, 블록 blob, 추가 blob, 테이블, 큐, 데이터베이스 및 관련 메타 데이터 저장소입니다. 저장소 개체 (blob, 등)는 단일 공유 내에서 개별적으로 포함 되므로, 각 개체의 최대 크기는 공유의 크기를 초과할 수 없습니다. 새 개체의 최대 크기는 새 개체를 만들 때 사용 되지 않는 공간으로 공유에 남아 있는 용량에 따라 달라 집니다.

사용 가능한 공간에 작업을 공유 사용량이 적을 때 [회수](#reclaim-capacity) 공간 성공 또는 사용할 수 없는, Azure Stack 클라우드 운영자 수 [마이그레이션할](#migrate-a-container-between) 다른 하나의 공유에서 blob 컨테이너입니다.

- 컨테이너 및 blob에 대 한 자세한 내용은 참조 하세요. [Blob storage](azure-stack-key-features.md#blob-storage) 주요 기능 및 Azure Stack의 개념입니다.
- Azure Stack에서 blob storage를 사용 하 여 테 넌 트 사용자가 작업 하는 방법에 대 한 자세한 내용은 [Azure Stack 저장소 서비스](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services)합니다.


### <a name="containers"></a>컨테이너
테 넌 트 사용자는 다음 blob 데이터를 저장 하는 데 사용 되는 컨테이너를 만듭니다. 사용자는 컨테이너 blob 배치를 결정 하는 동안 저장소 서비스는 볼륨 컨테이너를 확인 하는 알고리즘을 사용 합니다. 알고리즘은 일반적으로 가장 사용 가능한 공간을 사용 하 여 볼륨을 선택합니다.  

Blob 컨테이너에 위치한, 후 더 많은 공간을 사용 하는 blob 증가할 수 있습니다. 기존 및 새 blob을 추가 하면 blob 증가 축소 하는 해당 컨테이너를 보유 하는 볼륨의 사용 가능한 공간입니다.  

컨테이너는 단일 공유에 제한 되지 않습니다. 컨테이너의 입력 컨테이너에 결합 된 blob 데이터를 사용 하 여 80% 이상의 사용 가능한 공간 증가 하는 경우 *오버플로* 모드입니다. 오버플로 모드에 있을 때 해당 컨테이너에서 만든 모든 새 blob은 충분 한 공간이 있는 다른 볼륨에 할당 됩니다. 오버플로 모드에서 컨테이너는 시간이 지남에 따라 여러 볼륨에 걸쳐 배포 되는 blob을 가질 수 있습니다.

80%에 만들어지고 다음 볼륨의 사용 가능한 공간의 90%를 사용 하면 시스템에 Azure Stack 관리자 포털에서 경고를 발생 시킵니다. 클라우드 운영자는 사용 가능한 저장소 용량을 검토 하 고 콘텐츠를 리 밸런스를 계획 해야 합니다. 저장소 서비스 디스크를 사용 하는 100% 이며 추가 경고 발생 하는 경우 작업을 중지 합니다.

### <a name="disks"></a>디스크
VM 디스크는 테 넌 트에서 컨테이너에 추가 됩니다 및 운영 체제 디스크를 포함 합니다. Vm은 데이터 디스크를 하나 이상 포함할 수도 있습니다. 두 유형의 디스크는 페이지 blob으로 저장 됩니다. 테 넌 트 하기 위한 지침을 VM의 성능 향상을 위해 별도 컨테이너로 각 디스크를 배치 하는 것입니다.
- VM에서 디스크 (페이지 blob)를 보유 하는 각 컨테이너에 디스크를 소유 하는 VM에 연결 된 컨테이너를 간주 됩니다.
- VM에서 디스크를 유지 하지 않으면 하는 컨테이너에는 무료 컨테이너도 간주 됩니다.

연결 된 컨테이너에 대 한 공간을 확보 하는 옵션 [제한 됩니다](#move-vm-disks)합니다.
> [!TIP]  
> 클라우드 운영자는 테 넌 트 컨테이너에 추가할 수 있는 virtual machines (Vm)에 연결 되는 디스크를 직접 관리 하지 않습니다. 그러나 저장소 공유에는 공간 관리를 계획할 때 디스크 컨테이너 및 공유에 연결 하는 방법을 이해 하는 사용할 수 있습니다.

## <a name="monitor-shares"></a>모니터 공유
사용 가능한 공간이 제한 된 경우 이해할 수 있도록 공유를 모니터링 하려면 PowerShell 또는 관리 포털을 사용 합니다. 포털을 사용 하면 공간 부족에 있는 공유에 대 한 경고가 표시 됩니다.    

### <a name="use-powershell"></a>PowerShell 사용
클라우드 운영자로 서는 PowerShell을 사용 하 여 공유의 저장소 용량을 모니터링할 수 있습니다 **Get AzsStorageShare** cmdlet. AzsStorageShare Get cmdlet 공유의 각 바이트의 총, 할당 및 사용 가능한 공간을 반환합니다.   
![예제: 공유에 대 한 사용 가능한 공간 반환](media/azure-stack-manage-storage-shares/free-space.png)

- **총 용량** 공유에 사용할 수 있는 바이트의 총 공간입니다. 이 공간 데이터 및 저장소 서비스에서 유지 되는 메타 데이터에 사용 됩니다.
- **사용 된 용량** 테 넌 트 데이터와 연결 된 메타 데이터를 저장 하는 파일의 모든 범위에서 사용 되는 바이트의 데이터 양입니다.

### <a name="use-the-administrator-portal"></a>관리자 포털 사용
클라우드 운영자로 서 모든 공유의 저장소 용량을 보려면 관리 포털을 사용할 수 있습니다.

1. 에 로그인 합니다 [관리자 포털](https://adminportal.local.azurestack.external)합니다.
2. 선택 **모든 서비스** > **저장소** 사용량 정보를 볼 수 있는 파일 공유 목록을 엽니다. 

  ![예제: 저장소 파일 공유](media/azure-stack-manage-storage-shares/storage-file-shares.png)

  - **총** 공유에 사용할 수 있는 바이트의 총 공간입니다. 이 공간 데이터 및 저장소 서비스에서 유지 되는 메타 데이터에 사용 됩니다.
  - **사용 되는** 테 넌 트 데이터와 연결 된 메타 데이터를 저장 하는 파일의 모든 범위에서 사용 되는 바이트의 데이터 양입니다.

### <a name="storage-space-alerts"></a>저장소 공간 경고
관리 포털을 사용 하면 공간 부족에 있는 공유에 대 한 경고가 표시 됩니다.

> [!IMPORTANT]
> 클라우드 운영자로 서 전체 사용량에 도달 하지 못하도록 공유를 유지 합니다. 공유 경우 100% 사용, 저장소 서비스 더 이상 해당 공유에 대 한 함수입니다. 사용 가능한 공간을 복구 하 고 100%를 사용 하는 공유에 대 한 작업을 복원 하려면 Microsoft 지원에 문의 해야 합니다.

**경고**: 파일 공유를 사용 하는 80%를 초과, 수신 된 *경고* 관리 포털에서 경고: ![예: 경고](media/azure-stack-manage-storage-shares/alert-warning.png)


**높음**: 파일 공유 사용 90%를 초과 하는 경우 수신 된 *위험* 관리 포털에서 경고: ![예: 중요 한 경고](media/azure-stack-manage-storage-shares/alert-critical.png)

**세부 정보를 보려면**: 관리 포털에서 완화 옵션을 보려면 경고에 대 한 세부 정보를 열 수 있습니다. ![예: 경고 세부 정보 보기](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>사용 가능한 공간 관리
사용 가능한 공간을 공유 하는 데 필요한 경우 먼저 최소 적인 메서드를 사용 합니다. 예를 들어, 컨테이너를 마이그레이션하도록 선택 하기 전에 공간을 확보 하려고 합니다.  

### <a name="reclaim-capacity"></a>용량을 회수
*이 옵션 다중 노드 배포와 Azure Stack 개발 키트 둘 다에 적용 됩니다.*

삭제 된 테 넌 트 계정을 사용 하는 용량을 회수할 수 있습니다. 이 용량을 자동으로 회수 하는 경우 데이터 [보존 기간](azure-stack-manage-storage-accounts.md#set-the-retention-period) 에 도달 하면 즉시 회수 하기 위해 작업을 수행할 수 있습니다.

자세한 내용은 참조 하세요. [용량을 회수](azure-stack-manage-storage-accounts.md#reclaim) 에서 저장소 리소스를 관리 합니다.

### <a name="migrate-a-container-between-volumes"></a>볼륨 간에 컨테이너 마이그레이션
*이 옵션은 다중 노드 배포에만 적용 됩니다.*

테 넌 트 사용 패턴으로 인해 일부 테 넌 트 공유는 다른 항목 보다 더 많은 공간을 사용합니다. 상대적으로 사용 되지 않는 다른 공유 하기 전에 공간이 부족 하는 공유 될 수 있습니다.

다른 공유에 일부 blob 컨테이너를 수동으로 마이그레이션하여 남용된 공유에 공간을 확보 하려면 시도할 수 있습니다. 여러 작은 컨테이너의 용량이 모두 저장할 수 있는 단일 공유를 마이그레이션할 수 있습니다. 마이그레이션을 사용 하 여 이동할 *무료* 컨테이너입니다. 사용 가능한 컨테이너는 VM에 대 한 디스크를 포함 하지 않는 컨테이너입니다.   

새 공유에서 모든 blob 컨테이너를 통합 하는 마이그레이션.

- 컨테이너 오버플로 모드가 시작 되었으면 하 고이 blob 추가 볼륨에 배치 하는 경우 새 공유 모든 마이그레이션한 컨테이너 blob을 저장할 충분 한 용량이 있어야 합니다. 추가 공유에 있는 blob을 포함 합니다.

- PowerShell cmdlet *Get AzsStorageContainer* 컨테이너에 대 한 초기 볼륨에서 사용 중인 공간만 식별 합니다. Cmdlet는 추가 볼륨에 배치 하는 blob에 의해 사용 되는 공간을 식별 하지 않습니다. 따라서 컨테이너의 전체 크기 확인할 수 있습니다. 새 공유에 있는 컨테이너의 통합에 보낼 수 있습니다 새 공유 하는 오버플로 조건을 추가 공유에 데이터 배치는 가능성이 있습니다. 결과적으로, 다시 공유를 리 밸런스에 해야 할 수 있습니다.

- 리소스 그룹에 권한이 부족 하 고 오버플로 데이터에 대 한 추가 볼륨을 쿼리 하는 데 PowerShell을 사용할 수 없습니다 하는 경우 해당 리소스 그룹 및 컨테이너의 데이터의 총 크기를 이해 하려면 해당 데이터를 마이그레이션하기 전에 마이그레이션 소유자를 사용 하 여 작동 합니다.  

> [!IMPORTANT]
> Blob 컨테이너에 대 한 마이그레이션의 powershell을 사용 해야 하는 오프 라인 작업입니다. 마이그레이션 완료 될 때까지 마이그레이션하는 컨테이너에 대 한 모든 blob 오프 라인 상태로 유지 하 고 사용할 수 없습니다. 모든 진행 중인 마이그레이션 완료 될 때까지 Azure Stack 업그레이드 피해 야 합니다.

#### <a name="to-migrate-containers-using-powershell"></a>PowerShell을 사용 하 여 컨테이너를 마이그레이션하려면
1. 했는지 확인 [Azure PowerShell 설치 및 구성](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)합니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](https://go.microsoft.com/fwlink/?LinkId=394767)을 참조하세요.
2.  마이그레이션하려는 공유에 데이터를 이해 하려면 컨테이너를 검사 합니다. 볼륨의 마이그레이션에 대 한 최상의 후보 컨테이너를 식별 하려면 사용 합니다 **Get AzsStorageContainer** cmdlet:

    ```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    $shares = Get-AzsStorageShare -FarmName $farm_name
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
    ```
    $Containers를 검사 합니다.

    ```PowerShell
    $containers
    ```

    ![예: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  마이그레이션한 컨테이너를 보유할 최상의 대상 공유를 확인 합니다.

    ```PowerShell
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```

    $Destinationshares를 검사 합니다.

    ```PowerShell 
    $destinationshares
    ```

    ![예: $destination 공유](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. 컨테이너에 대 한 마이그레이션을 시작 합니다. 마이그레이션은 비동기입니다. 첫 번째 마이그레이션 완료 되기 전에 마이그레이션의 추가 컨테이너를 시작 하는 경우 각각의 상태를 추적 하려면 작업 id를 사용 합니다.

  ```PowerShell
  $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
  ```

  그런 다음 $jobId를 검사 합니다. 다음 예제에서는 바꿉니다 *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* 확인 하려면 작업 id를 사용 하 여:

  ```PowerShell
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```

5. 작업 id를 사용 하 여 마이그레이션 작업의 상태를 확인 합니다. 컨테이너 마이그레이션이 완료 되 면 **MigrationStatus** 로 설정 된 **완료**합니다.

  ```PowerShell 
  Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
  ```

  ![예제: 마이그레이션 상태](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  진행 중인 마이그레이션 작업을 취소할 수 있습니다. 마이그레이션 작업은 비동기적으로 처리를 취소 합니다. $Jobid를 사용 하 여 취소를 추적할 수 있습니다.

  ```PowerShell
  Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
  ```

  ![예제: 롤백 상태](media/azure-stack-manage-storage-shares/rollback.png)

7. 명령을 실행할 수 있습니다는 6 단계에서 다시 마이그레이션 작업은 상태를 확인할 때까지 **Canceled**:  

    ![예제: 취소 된 상태](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM 디스크를 이동 합니다.
*이 옵션은 다중 노드 배포에만 적용 됩니다.*

공간 관리를 위한 가장 극단적인 방법은 가상 머신 디스크의 이동 합니다. (하나는 VM 디스크를 포함 하는) 연결 된 컨테이너를 이동 하는 것은 복잡 한 이므로이 동작을 수행 하려면 Microsoft 지원에 문의 합니다.

## <a name="next-steps"></a>다음 단계
에 대해 자세히 알아보세요 [가상 컴퓨터 사용자에 게 제공](azure-stack-tutorial-tenant-vm.md)합니다.
