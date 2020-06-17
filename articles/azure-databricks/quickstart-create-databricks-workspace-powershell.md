---
title: 빠른 시작 - PowerShell을 사용하여 Azure Databricks 작업 영역 만들기
description: 이 빠른 시작에서는 PowerShell을 사용하여 Azure Databricks 작업 영역을 만드는 방법을 보여 줍니다.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485691"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Databricks 작업 영역을 만듭니다.

이 빠른 시작에서는 PowerShell을 사용하여 Azure Data 작업 영역 만드는 방법을 설명합니다. PowerShell을 사용하여 대화형으로 또는 스크립트에서 Azure 리소스를 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> Az.Databricks PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.Databricks -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.Databricks PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

Azure Databricks를 처음 사용하는 경우 **Microsoft.Databricks** 리소스 공급자를 등록해야 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독 ID를 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 **myresourcegroup**이라는 리소스 그룹을 **미국 서부 2** 지역에 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 PowerShell을 사용하여 Azure Databricks 작업 영역을 만듭니다.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

다음 값을 제공합니다.

|       **속성**       |                                                                                **설명**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Name                     | Databricks 작업 영역의 이름을 제공합니다.                                                                                                                                   |
| ResourceGroupName        | 기존 리소스 그룹 이름 지정                                                                                                                                        |
| 위치                 | **미국 서부 2**를 선택합니다. 사용 가능한 다른 영역은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.                                     |
| ManagedResourceGroupName | 새 관리 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다.                                                                                        |
| SKU                      | **표준**, **프리미엄** 또는 **평가판** 중에서 선택합니다. 이러한 계층에 대한 자세한 내용은 [Databricks 가격 책정](https://azure.microsoft.com/pricing/details/databricks/)을 참조하세요. |

작업 영역 생성에는 몇 분 정도가 소요됩니다. 이 프로세스가 완료되면 사용자 계정이 작업 영역에서 관리 사용자로 자동으로 추가됩니다.

작업 영역 배포가 실패해도 작업 영역은 여전히 실패한 상태로 생성됩니다. 실패한 작업 영역을 삭제하고 배포 오류를 해결하는 새 작업 영역을 만듭니다. 실패한 작업 영역을 삭제하면 관리되는 리소스 그룹과 성공적으로 배포된 리소스도 모두 삭제됩니다.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Databricks 작업 영역의 프로비전 상태를 확인합니다.

Databricks 작업 영역이 성공적으로 프로 비전되었는지 확인하려면 `Get-AzDatabricksWorkspace` cmdlet을 사용할 수 있습니다.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스가 다른 빠른 시작 또는 자습서에 필요하지 않은 경우 다음 예제를 실행하여 해당 리소스를 삭제할 수 있습니다.

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 이 빠른 시작의 범위에 속하지 않는 리소스가 지정된 리소스 그룹에 있는 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

리소스 그룹을 삭제하지 않고 이 빠른 시작에서 만든 서버만 삭제하려면 `Remove-AzDatabricksWorkspace` cmdlet을 사용합니다.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Databricks에서 Spark 클러스터 만들기](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
