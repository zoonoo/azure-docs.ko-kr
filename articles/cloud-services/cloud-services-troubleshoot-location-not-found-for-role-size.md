---
title: Azure에 클라우드 서비스 (클래식)를 배포할 때 LocationNotFoundForRoleSize 문제 해결 | Microsoft Docs
description: 이 문서에서는 클라우드 서비스 (클래식)를 Azure에 배포할 때 LocationNotFoundForRoleSize 예외를 해결 하는 방법을 보여 줍니다.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 3d1a1135db0421d89d4c6c9f278c86e02cb5cb32
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558961"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Azure에 클라우드 서비스 (클래식)를 배포할 때 LocationNotFoundForRoleSize 문제 해결

이 문서에서는 Azure 클라우드 서비스 (클래식)를 배포할 때 VM (가상 머신) 크기를 사용할 수 없는 할당 오류를 해결 합니다.

클라우드 서비스 (클래식)에 인스턴스를 배포 하거나 새 웹 또는 작업자 역할 인스턴스를 추가 하는 경우 Microsoft Azure 계산 리소스를 할당 합니다.

Azure 구독 제한에 도달 하기 전에도 이러한 작업 중에 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

Azure Portal에서 클라우드 서비스 (클래식)로 이동 하 고 사이드바에서 *작업 로그 (클래식)* 를 선택 하 여 로그를 확인 합니다.

![이미지 작업 로그 (클래식) 블레이드를 표시 합니다.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

클라우드 서비스 (클래식)의 로그를 검사 하는 경우 다음과 같은 예외가 표시 됩니다.

|예외 유형  |오류 메시지  |
|---------|---------|
|LocationNotFoundForRoleSize     |' ' 작업이 `{Operation ID}` 실패 했습니다. ' 요청 된 VM 계층은 현재 `{Region ID}` 이 구독에 대해 지역 ()에서 사용할 수 없습니다. 다른 계층을 시도 하거나 다른 위치에 배포 하세요. '.|

## <a name="cause"></a>원인

배포 하는 지역 또는 클러스터에는 용량 문제가 있습니다. *LocationNotFoundForRoleSize* 예외는 지정한 지역에 대해 선택한 리소스 SKU (VM 크기)를 사용할 수 없는 경우에 발생 합니다.

## <a name="solution"></a>해결 방법

이 시나리오에서는 클라우드 서비스 (클래식)를 배포할 다른 지역 또는 SKU를 선택 해야 합니다. 클라우드 서비스 (클래식)를 배포 하거나 업그레이드 하기 전에 지역 또는 가용성 영역에서 사용할 수 있는 Sku를 확인할 수 있습니다. 아래 [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)또는 [REST API](#list-skus-in-region-using-rest-api) 프로세스를 따르세요.

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLI를 사용 하 여 지역에서 Sku 나열

[Az vm list-sku](/cli/azure/vm.html#az_vm_list_skus) 명령을 사용할 수 있습니다.

- 사용 중인 `--location` 위치에 대 한 출력을 필터링 하려면 매개 변수를 사용 합니다.
- `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다.
- 자세한 내용은 [사용할 수 없는 SKU에 대 한 오류 해결](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) 가이드를 참조 하세요.

    **예를 들어:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **예제 결과:** ![ ' Az vm southcentralus--location--size Standard_F--output table ' 명령을 실행 하 여 사용 가능한 Sku를 표시 하는 출력을 Azure CLI 합니다.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell을 사용 하 여 지역에서 Sku 나열

[AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용할 수 있습니다.

- 결과를 위치별로 필터링합니다.
- 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.
- 자세한 내용은 [사용할 수 없는 SKU에 대 한 오류 해결](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) 가이드를 참조 하세요.

**예를 들어:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**몇 가지 다른 유용한 명령은 다음과 같습니다.**

크기 (Standard_DS14_v2)를 포함 하는 위치를 필터링 합니다.

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

크기 (V3)를 포함 하는 모든 위치를 필터링 합니다.

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>REST API를 사용 하 여 지역에서 Sku 나열

[리소스 Sku 목록](/rest/api/compute/resourceskus/list) 작업을 사용할 수 있습니다. 다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

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

추가 할당 오류 솔루션 및 생성 방법을 더 잘 이해 하려면 다음을 수행 합니다.

> [!div class="nextstepaction"]
> [할당 오류-클라우드 서비스 (클래식)](cloud-services-allocation-failures.md)

이 문서에서 Azure 문제가 해결 되지 않으면 MSDN에서 Azure 포럼을 방문 [하 여 Stack Overflow](https://azure.microsoft.com/support/forums/)합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.