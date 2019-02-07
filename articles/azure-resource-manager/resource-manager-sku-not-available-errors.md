---
title: Azure SKU 사용할 수 없음 오류 | Microsoft Docs
description: 배포 시 발생하는 SKU를 사용할 수 없음 오류를 해결하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 4688acbb2742579e0f9f3fbb2604ffd8ef12bfd5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081044"
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU 사용할 수 없음 오류 해결

이 문서에서는 **SkuNotAvailable** 오류를 해결하는 방법에 대해 설명합니다. 해당 위치 또는 대체 위치에서 비즈니스 요구를 충족하는 적합한 SKU를 찾을 수 없는 경우 [SKU 요청](https://aka.ms/skurestriction)을 Azure 지원에 제출하세요.


## <a name="symptom"></a>증상

리소스를 배포할 때(일반적으로 가상 머신) 다음 오류 코드 및 오류 메시지가 표시됩니다.

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>원인

선택한 리소스 SKU(예: VM 크기)를 선택한 위치에 사용할 수 없는 경우 이 오류가 나타납니다.

## <a name="solution-1---powershell"></a>해결 방법 1 - PowerShell

한 지역에서 사용할 수 있는 SKU를 확인하려면 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용합니다. 결과를 위치별로 필터링합니다. 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

결과에는 위치에 대한 SKU 목록과 해당 SKU에 대한 제한 사항이 포함됩니다. SKU는 `NotAvailableForSubscription`으로 나열될 수 있습니다.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>해결 방법 2 - Azure CLI

지역에서 사용할 수 있는 SKU를 확인하려면 `az vm list-skus` 명령을 사용합니다. `--location` 매개 변수를 사용하여 출력을 사용 중인 위치로 필터링합니다. `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

이 명령은 다음과 같은 결과를 반환합니다.

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>해결 방법 3 - Azure Portal

지역에서 사용할 수 있는 SKU를 확인하려면 [포털](https://portal.azure.com)을 사용합니다. 포털에 로그인하고 인터페이스를 통해 리소스를 추가합니다. 값을 설정하면 해당 리소스에 사용 가능한 SKU가 표시됩니다. 배포를 완료할 필요는 없습니다.

예를 들어, 가상 머신을 만드는 프로세스를 시작합니다. 사용 가능한 다른 크기를 보려면 **크기 변경**을 선택합니다.

![VM 만들기](./media/resource-manager-sku-not-available-errors/create-vm.png)

필터링하고 스크롤하면서 사용 가능한 크기를 선택할 수 있습니다.

![사용 가능한 SKU](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>해결 방법 4 - REST

지역에서 사용할 수 있는 SKU를 확인하려면 [리소스 SKU - 목록](/rest/api/compute/resourceskus/list) 작업을 사용합니다.

다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

