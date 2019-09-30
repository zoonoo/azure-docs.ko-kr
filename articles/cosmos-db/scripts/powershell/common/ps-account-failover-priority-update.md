---
title: Azure PowerShell 스크립트 - Azure Cosmos 계정에 대한 장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos 계정에 대한 장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: e4406124a7ea4eac213d830d0e5960e76fb6d364
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155399"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos 계정에 대한 장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트

> [!NOTE]
> `failoverPriority=0`이 포함된 지역에 대한 변경 내용은 수동 장애 조치(failover)를 트리거하고 수동 장애 조치(failover)를 위해 구성된 계정으로만 수행할 수 있습니다. 다른 모든 지역에 대한 변경 내용은 Cosmos 계정의 장애 조치(failover) 우선순위를 변경하기만 하면 됩니다.
> [!NOTE]
> 이 샘플에서는 SQL(Core) API 계정을 사용하는 방법을 보여줍니다. 이 샘플을 다른 API에 사용하려면 관련 속성을 복사하고 API별 스크립트에 적용합니다.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure 리소스**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | 리소스에서 작업을 호출합니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../../../powershell-samples.md)에 있습니다.
