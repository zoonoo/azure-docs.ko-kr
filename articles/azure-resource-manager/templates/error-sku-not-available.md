---
title: SKU를 사용할 수 없음 오류
description: Azure Resource Manager를 사용 하 여 리소스를 배포할 때 SKU에서 사용할 수 없음 오류 문제를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 37ad70093cce4cea6689fecbc3b6ccb91db5017b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333225"
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU 사용할 수 없음 오류 해결

이 문서에서는 **SkuNotAvailable** 오류를 해결하는 방법에 대해 설명합니다. 해당 지역/영역 또는 비즈니스 요구 사항을 충족 하는 대체 지역/영역에서 적합 한 SKU를 찾을 수 없는 경우 Azure 지원에 [sku 요청](https://aka.ms/skurestriction) 을 제출 하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>증상

리소스를 배포할 때(일반적으로 가상 머신) 다음 오류 코드 및 오류 메시지가 표시됩니다.

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>원인

선택한 리소스 SKU(예: VM 크기)를 선택한 위치에 사용할 수 없는 경우 이 오류가 나타납니다.

Azure 스폿 VM 또는 별색 확장 집합 인스턴스를 배포 하는 경우이 위치에 Azure 지점에 대 한 용량이 없습니다. 자세한 내용은 [지점 오류 메시지](../../virtual-machines/error-codes-spot.md)를 참조 하세요.

## <a name="solution-1---powershell"></a>해결 방법 1 - PowerShell

지역/영역에서 사용할 수 있는 Sku를 확인 하려면 [AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용 합니다. 결과를 위치별로 필터링합니다. 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

결과에는 위치에 대한 SKU 목록과 해당 SKU에 대한 제한 사항이 포함됩니다. SKU는 `NotAvailableForSubscription`으로 나열될 수 있습니다.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

몇 가지 추가 샘플:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

끝에 "fc"를 추가 하면 더 자세한 정보가 반환 됩니다.

## <a name="solution-2---azure-cli"></a>해결 방법 2 - Azure CLI

지역에서 사용할 수 있는 SKU를 확인하려면 `az vm list-skus` 명령을 사용합니다. `--location` 매개 변수를 사용하여 출력을 사용 중인 위치로 필터링합니다. `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

이 명령은 다음과 같은 결과를 반환합니다.

```output
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

![VM 만들기](./media/error-sku-not-available/create-vm.png)

필터링하고 스크롤하면서 사용 가능한 크기를 선택할 수 있습니다.

![사용 가능한 SKU](./media/error-sku-not-available/available-sizes.png)

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

