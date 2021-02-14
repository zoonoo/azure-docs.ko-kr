---
title: Azure에 클라우드 서비스를 배포할 때 ConstrainedAllocationFailed 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure에 클라우드 서비스를 배포할 때 ConstrainedAllocationFailed 예외를 해결 하는 방법을 보여 줍니다.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521070"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Azure에 클라우드 서비스를 배포할 때 ConstrainedAllocationFailed 문제 해결

이 문서에서는 제약 조건으로 인해 Azure Cloud Services를 배포할 수 없는 할당 오류를 해결 합니다.

다음과 같은 경우 Microsoft Azure 할당 합니다.

- Cloud services 인스턴스 업그레이드

- 새 웹 또는 작업자 역할 인스턴스 추가

- 클라우드 서비스에 인스턴스 배포

Azure 구독 제한에 도달 하기 전에도 이러한 작업 중에 오류가 발생할 수 있습니다.

> [!TIP]
> 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

## <a name="symptom"></a>증상

Azure Portal에서 클라우드 서비스로 이동 하 고 사이드바에서 *작업 로그 (클래식)* 를 선택 하 여 로그를 확인 합니다.

클라우드 서비스의 로그를 검사할 때 다음과 같은 예외가 표시 됩니다.

|예외 유형  |오류 메시지  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 작업 ' `{Operation ID}` '이 (가) ConstrainedAllocationFailed 코드와 함께 실패 했습니다. 세부 정보: 할당 하지 못했습니다. 요청에서 제약 조건을 충족할 수 없습니다. 요청된 새로운 서비스 배포가 선호도 그룹에 바인딩되어 있거나 가상 네트워크를 대상으로 하거나 호스티드 서비스에 기존 배포가 있습니다. 이러한 조건이 특정 Azure 리소스에 대한 새로운 배포를 제한합니다. 나중에 다시 시도 하거나, VM 크기나 역할 인스턴스의 수를 줄여 보세요. 가능하다면 앞서 말한 제약 조건을 제거하거나 다른 지역에 배포를 시도합니다.|

## <a name="cause"></a>원인

배포 하는 지역 또는 클러스터에는 용량 문제가 있습니다. 선택한 리소스 SKU를 지정한 위치에 사용할 수 없는 경우에 발생 합니다.

> [!NOTE]
> 클라우드 서비스의 첫 번째 노드가 배포 되 면 리소스 풀에 *고정* 됩니다. 리소스 풀은 단일 클러스터 또는 클러스터 그룹 일 수 있습니다.
>
> 시간이 지남에 따라이 리소스 풀의 리소스는 완전히 활용 될 수 있습니다. 고정 된 리소스 풀에서 사용할 수 있는 리소스가 부족 한 경우 클라우드 서비스에서 추가 리소스에 대 한 할당 요청을 수행 하면이 요청으로 인해 [할당 오류가](cloud-services-allocation-failures.md)발생 합니다.

## <a name="solution"></a>해결 방법

이 시나리오에서는 클라우드 서비스를 배포할 다른 지역 또는 SKU를 선택 해야 합니다. 클라우드 서비스를 배포 하거나 업그레이드 하기 전에 지역 또는 가용성 영역에서 사용할 수 있는 Sku를 확인할 수 있습니다. 아래 [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)또는 [REST API](#list-skus-in-region-using-rest-api) 프로세스를 따르세요.

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLI를 사용 하 여 지역에서 Sku 나열

[Az vm list-sku](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) 명령을 사용할 수 있습니다.

- 사용 중인 `--location` 위치에 대 한 출력을 필터링 하려면 매개 변수를 사용 합니다.
- `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다.
- 자세한 내용은 [사용할 수 없는 SKU에 대 한 오류 해결](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) 가이드를 참조 하세요.

    **예를 들어:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **예제 결과:** ![ ' Az vm southcentralus--location--size Standard_F--output table ' 명령을 실행 하 여 사용 가능한 Sku를 표시 하는 출력을 Azure CLI 합니다.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell을 사용 하 여 지역에서 Sku 나열

[AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용할 수 있습니다.

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

[리소스 Sku 목록](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 작업을 사용할 수 있습니다. 다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>다음 단계

추가 할당 오류 솔루션 및 생성 방법을 더 잘 이해 하려면 다음을 수행 합니다.

> [!div class="nextstepaction"]
> [할당 실패 (클라우드 서비스)](cloud-services-allocation-failures.md)

이 문서에서 Azure 문제가 해결 되지 않으면 MSDN에서 Azure 포럼을 방문 [하 여 Stack Overflow](https://azure.microsoft.com/support/forums/)합니다. 이러한 포럼이나 [@AzureSupport Twitter](https://twitter.com/AzureSupport) 의 에 문제를 게시할 수 있습니다. 또한 Azure 지원 요청을 제출할 수 있습니다. 지원 요청을 제출하려면 [Azure 지원](https://azure.microsoft.com/support/options/) 페이지에서 *지원 받기* 를 선택합니다.
