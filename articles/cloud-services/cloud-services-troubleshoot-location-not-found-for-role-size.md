---
title: Azure에 클라우드 서비스(클래식)를 배포하는 경우 LocationNotFoundForRoleSize 문제 해결 | Microsoft Docs
description: 이 문서에서는 클라우드 서비스(클래식)를 Azure에 배포할 때 LocationNotFoundForRoleSize 예외 사항을 해결하는 방법을 보여 줍니다.
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f45e28e2c7a816f923d1af579b818e546c992717
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821494"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Azure에 클라우드 서비스(클래식)를 배포하는 경우 LocationNotFoundForRoleSize 문제 해결

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

이 문서에서는 Azure Cloud Service(클래식)를 배포할 때 VM(가상 머신) 크기를 사용할 수 없는 할당 오류 문제를 해결합니다.

클라우드 서비스(클래식)에 인스턴스를 배포하거나 새 웹 또는 작업자 역할 인스턴스를 추가할 때 Microsoft Azure는 컴퓨팅 리소스를 할당합니다.

Azure 구독 제한에 도달하기 전에도 이러한 작업 중에 간혹 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

Azure Portal에서 클라우드 서비스(클래식)로 이동하여 사이드바에서 ‘작업 로그(클래식)’를 선택하고 로그를 확인합니다.

![작업 로그(클래식) 블레이드를 보여 주는 이미지입니다.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

클라우드 서비스(클래식)의 로그를 검사하는 경우 다음과 같은 예외가 표시됩니다.

|예외 유형  |오류 메시지  |
|---------|---------|
|LocationNotFoundForRoleSize     |'`{Operation ID}`' 작업 실패: '요청한 VM 계층은 현재 이 구독의 지역(`{Region ID}`)에서 사용할 수 없습니다. 다른 계층을 시도하거나 다른 위치에 배포하세요.'|

## <a name="cause"></a>원인

배포하는 지역 또는 클러스터에 용량 문제가 있습니다. *LocationNotFoundForRoleSize* 예외는 지정한 지역에 대해 선택한 리소스 SKU(VM 크기)를 사용할 수 없는 경우에 발생합니다.

## <a name="solution"></a>솔루션

이 시나리오에서는 클라우드 서비스(클래식)를 배포할 다른 지역 또는 SKU를 선택해야 합니다. 클라우드 서비스(클래식)를 배포하거나 업그레이드하기 전에 지역 또는 가용성 영역에서 사용할 수 있는 SKU를 확인할 수 있습니다. 아래의 [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell) 또는 [REST API](#list-skus-in-region-using-rest-api) 프로세스를 따르세요.

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLI를 사용하여 지역에서 SKU 나열

[az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) 명령을 사용할 수 있습니다.

- 사용 중인 위치에 대한 출력을 필터링하려면 `--location` 매개 변수를 사용합니다.
- `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다.
- 자세한 내용은 [사용할 수 없는 SKU에 대한 오류 해결](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) 가이드를 참조하세요.

    **예를 들면 다음과 같습니다.**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **예제 결과:** ![사용 가능한 SKU를 보여 주는 'az vm list-skus --location southcentralus --size Standard_F --output table' 명령을 실행하는 Azure CLI 출력입니다.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell을 사용하여 지역에서 SKU 나열

[AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용할 수 있습니다.

- 결과를 위치별로 필터링합니다.
- 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.
- 자세한 내용은 [사용할 수 없는 SKU에 대한 오류 해결](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) 가이드를 참조하세요.

**예를 들면 다음과 같습니다.**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**기타 유용한 명령:**

크기(Standard_DS14_v2)를 포함하는 위치 필터링:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

크기(V3)를 포함하는 모든 위치 필터링:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>REST API를 사용하여 지역에서 SKU 나열

[리소스 SKU - 나열](/rest/api/compute/resourceskus/list) 작업을 사용할 수 있습니다. 다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>다음 단계

추가 할당 오류 솔루션 및 생성 방법을 더욱 쉽게 이해하려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [할당 오류 - 클라우드 서비스(클래식)](cloud-services-allocation-failures.md)

현재 Azure 문제와 관련된 정보가 이 문서에 없을 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼에 방문하세요. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.
