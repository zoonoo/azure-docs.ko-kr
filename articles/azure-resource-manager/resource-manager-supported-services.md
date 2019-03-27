---
title: Azure 리소스 공급자 및 리소스 종류 | Microsoft Docs
description: 리소스 관리자, 스키마, 제공되는 API 버전 및 리소스를 호스팅할 수 있는 지역을 지원하는 리소스 공급자에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: tomfitz
ms.openlocfilehash: 520aeb8e47b5e94e6346e682f21f46cb0814f8f3
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445451"
---
# <a name="azure-resource-providers-and-types"></a>Azure 리소스 공급자 및 종류

리소스를 배포할 때는 리소스 공급자 및 형식에 대한 정보를 자주 검색하게 됩니다. 이 문서에서는 다음 방법을 설명합니다.

* Azure의 모든 리소스 공급자 보기
* 리소스 공급자의 등록 상태 확인
* 리소스 공급자 등록
* 리소스 공급자에 대한 리소스 종류 보기
* 리소스 종류에 대한 유효한 위치 보기
* 리소스 종류에 대한 유효한 API 버전 보기

Azure portal, Azure PowerShell 또는 Azure CLI를 통해 이러한 단계를 수행할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

모든 리소스 공급자와 구독 등록 상태를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택합니다.

    ![구독 선택](./media/resource-manager-supported-services/select-subscriptions.png)
3. **모든 서비스**  상자에서 **구독**을 입력한 다음, **구독**을 선택합니다.
4. 구독 목록에서 보려는 구독을 선택합니다.
5. **리소스 공급자**를 선택하고 사용 가능한 리소스 공급자의 목록을 봅니다.

    ![리소스 공급자 보기](./media/resource-manager-supported-services/show-resource-providers.png)

6. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. 등록 범위는 항상 해당 구독입니다. 기본적으로 대부분의 리소스 공급자는 자동으로 등록됩니다. 그러나 일부 리소스 공급자는 수동으로 등록해야 할 수도 있습니다. 리소스 공급자를 등록 하려면 작업을 수행 하는 권한이 있어야 합니다 `/register/action` 리소스 공급자에 대 한 작업입니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하려면 **등록**을 선택합니다. 이전 스크린샷에는 **Microsoft.Blueprint**에 대한 **등록** 링크가 강조 표시되어 있습니다.

    구독에서 해당 리소스 공급자의 리소스 종류를 여전히 있는 경우에 리소스 공급자를 등록 취소할 수 없습니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/resource-manager-supported-services/more-services.png)

3. **모든 서비스** 상자에서 **리소스 탐색기**를 입력한 다음, **리소스 탐색기**를 선택합니다.
4. 오른쪽 화살표를 선택하여 **공급자**를 확장합니다.

    ![공급자 선택](./media/resource-manager-supported-services/select-providers.png)

5. 보려는 리소스 공급자 및 리소스 종류를 펼칩니다.

    ![리소스 종류 선택](./media/resource-manager-supported-services/select-resource-type.png)

6. 리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하도록 구독에 대한 제한 사항이 있을 수 있습니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 위치가 표시됩니다.

    ![위치 표시](./media/resource-manager-supported-services/show-locations.png)

7. API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다. 리소스 탐색기에는 리소스 종류에 대한 유효한 API 버전이 표시됩니다.

    ![API 버전 표시](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

반환 결과는 다음과 비슷합니다.

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. 등록 범위는 항상 해당 구독입니다. 기본적으로 대부분의 리소스 공급자는 자동으로 등록됩니다. 그러나 일부 리소스 공급자는 수동으로 등록해야 할 수도 있습니다. 리소스 공급자를 등록 하려면 작업을 수행 하는 권한이 있어야 합니다 `/register/action` 리소스 공급자에 대 한 작업입니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

구독에서 해당 리소스 공급자의 리소스 종류를 여전히 있는 경우에 리소스 공급자를 등록 취소할 수 없습니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

반환하는 내용은 다음과 같습니다.

```powershell
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

반환하는 내용은 다음과 같습니다.

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하도록 구독에 대한 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

반환하는 내용은 다음과 같습니다.

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure의 모든 리소스 공급자와 구독에 대한 등록 상태를 보려면 다음을 사용합니다.

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

반환 결과는 다음과 비슷합니다.

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. 등록 범위는 항상 해당 구독입니다. 기본적으로 대부분의 리소스 공급자는 자동으로 등록됩니다. 그러나 일부 리소스 공급자는 수동으로 등록해야 할 수도 있습니다. 리소스 공급자를 등록 하려면 작업을 수행 하는 권한이 있어야 합니다 `/register/action` 리소스 공급자에 대 한 작업입니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다.

```azurecli
az provider register --namespace Microsoft.Batch
```

등록이 진행 중인 메시지를 반환합니다.

구독에서 해당 리소스 공급자의 리소스 종류를 여전히 있는 경우에 리소스 공급자를 등록 취소할 수 없습니다.

특정 리소스 공급자에 대한 정보를 보려면 다음을 사용합니다.

```azurecli
az provider show --namespace Microsoft.Batch
```

반환 결과는 다음과 비슷합니다.

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

리소스 공급자에 대한 리소스 종류를 보려면 다음을 사용합니다.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

반환하는 내용은 다음과 같습니다.

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API 버전은 리소스 공급자가 릴리스하는 REST API 작업의 버전에 해당합니다. 리소스 공급자는 새 기능을 사용하도록 설정할 때 새 버전의 REST API를 릴리스합니다.

리소스 종류의 사용 가능한 API 버전을 가져오려면 다음을 사용합니다.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

반환하는 내용은 다음과 같습니다.

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

리소스 관리자는 모든 지역에서 지원되지만 배포한 리소스는 모든 지역에서 지원되지 않을 수 있습니다. 또한 해당 리소스를 지원하는 일부 지역을 사용하지 못하도록 구독에 대한 제한 사항이 있을 수 있습니다.

리소스 종류의 지원되는 위치를 가져오려면 다음을 사용합니다.

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

반환하는 내용은 다음과 같습니다.

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>다음 단계

* 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
* 리소스 공급자 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.
* 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](resource-group-template-deploy.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.
