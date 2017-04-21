---
title: "논리적 조직을 위한 Azure 리소스에 태그 지정 | Microsoft Docs"
description: "태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 2f56314769d90a1f0f9ebb5ece9c8e54b23b8936
ms.lasthandoff: 02/21/2017


---
# <a name="use-tags-to-organize-your-azure-resources"></a>태그를 사용하여 Azure 리소스 구성
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> 리소스 관리자 작업을 지원하는 리소스에만 태그를 적용할 수 있습니다. 클래식 배포 모델을 통해(예: 클래식 포털을 통해) 가상 컴퓨터, 가상 네트워크 또는 저장소를 만든 경우 해당 리소스에 태그를 적용할 수 없습니다. 태그 지정을 지원하려면 Resource Manager를 통해 이러한 리소스를 다시 배포해야 합니다. 다른 모든 리소스는 태그 지정을 지원합니다.
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>정책을 사용하여 태그 일관성 보장

리소스 정책을 통해 조직의 표준 규칙을 만들 수 있습니다. 적절한 값으로 리소스에 태그가 지정되도록 정책을 만들 수 있습니다. 자세한 내용은 [태그에 대한 리소스 정책 적용](resource-manager-policy-tags.md)을 참조하세요.

## <a name="templates"></a>템플릿

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>포털
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure CLI 2.0을 사용하면 리소스와 리소스 그룹에 태그를 추가하고 태그 값으로 리소스를 쿼리할 수 있습니다.

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 유지하려는 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다. 경우에 따라 태그를 추가하는 방법은 다음과 같습니다.

* 기존 태그를 유지하지 않고 리소스 그룹에 태그를 추가하는 경우.

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* 기존 태그를 유지하지 않고 리소스에 태그를 추가하는 경우.

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

이미 태그가 있는 리소스에 태그를 추가하려면 먼저 기존 태그를 검색합니다. 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

그러면 다음 형식이 반환됩니다.

```
Dept        : Finance
Environment : Test
```

기존 태그를 리소스에 다시 적용하고 새 태그를 추가합니다.

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

특정 태그가 있는 리소스 그룹을 가져오려면 `az group list`를 사용합니다.

```azurecli
az group list --tag Dept=IT
```

특정 태그 및 값이 있는 모든 리소스를 가져오려면 `az resource list`를 사용합니다.

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API
포털 및 PowerShell 둘 다 백그라운드에서 [리소스 관리자 REST API](https://docs.microsoft.com/rest/api/resources/) 를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 GET을 사용하여 태그를 얻고 PATCH 호출을 통해 태그 집합을 업데이트할 수 있습니다.

## <a name="tags-and-billing"></a>태그 및 청구
태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 서로 다른 조직에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화합니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용과 같이 런타임 환경으로 비용을 분류하는 데 태그를 사용할 수 있습니다.


[Azure 리소스 사용 및 RateCard API](../billing/billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com)에서 사용 현황 파일을 다운로드할 수 있습니다. 대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](../billing/billing-usage-rate-card-overview.md)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.


대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 **태그** 열에 태그가 나타납니다. 자세한 내용은 [Microsoft Azure 청구서 이해](../billing/billing-understand-your-bill.md)를 참조하세요.

![요금 청구에 대한 태그를 참조하십시오.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>다음 단계
* 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 정의한 정책을 사용하려면 모든 리소스에 특정 태그 값이 있어야 할 수도 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.
* 리소스 배포 시 Azure PowerShell 사용에 대한 소개는 [Azure 리소스 관리자에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
* 리소스 배포 시 Azure CLI 사용에 대한 소개는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.
* 포털 사용에 대한 소개는 [Azure 포털을 사용하여 Azure 리소스 관리](resource-group-portal.md)  
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


