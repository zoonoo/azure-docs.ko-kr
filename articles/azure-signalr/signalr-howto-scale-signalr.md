---
title: Azure SignalR 서비스의 인스턴스 확장
description: Azure Portal 또는 Azure CLI를 통해 용량을 추가하거나 줄이기 위해 Azure SignalR 서비스 인스턴스를 확장하는 방법을 알아봅니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659290"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Azure SignalR 서비스 인스턴스를 확장하는 방법은 무엇입니까?
이 문서에서는 Azure SignalR 서비스의 인스턴스를 확장하는 방법을 보여 줍니다. 확장, 확장 및 확장을 위한 두 가지 시나리오가 있습니다.

* [확장](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 더 많은 단위, 연결, 메시지 등을 가져옵니다. 가격 책정 계층을 무료에서 표준으로 변경하여 확장합니다.
* 확장 : SignalR 단위 수를 [늘립니다.](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) 최대 100단위로 확장할 수 있습니다.

배율 설정을 적용하는 데 몇 분 정도 걸릴 수 있습니다. 드문 경우지만 적용하는 데 약 30분이 걸릴 수 있습니다. 코드를 변경하거나 서버 응용 프로그램을 다시 배포할 필요가 없습니다.

개별 SignalR 서비스의 가격 및 용량에 대한 자세한 내용은 [Azure SignalR 서비스 가격 세부 정보를](https://azure.microsoft.com/pricing/details/signalr-service/)참조하십시오.  

> [!NOTE]
> SignalR 서비스를 **프리** 티어에서 **표준** 계층으로 변경하거나 그 반대로 변경하면 공용 서비스 IP가 변경되며 일반적으로 전체 인터넷을 통해 DNS 서버에 변경을 전파하는 데 30-60분이 걸립니다. DNS가 업데이트되기 전에 서비스에 연결할 수 없습니다. 일반적으로 가격 책정 계층을 너무 자주 변경하지 않는 것이 좋습니다.


## <a name="scale-on-azure-portal"></a>Azure 포털에서 확장

1. 브라우저에서 [Azure 포털을](https://portal.azure.com)엽니다.

2. SignalR 서비스 페이지에서 왼쪽 메뉴에서 **배율을**선택합니다.
   
3. 가격 책정 계층을 선택한 다음 **선택**을 클릭합니다. **표준** 계층의 단위 수를 설정합니다.
   
    ![포털에서 확장](./media/signalr-howto-scale/signalr-howto-scale.png)

4. **저장**을 클릭합니다.

## <a name="scale-using-azure-cli"></a>Azure CLI를 사용한 크기 조정

이 스크립트는 **프리** 티어와 새 리소스 그룹의 새 SignalR 서비스 리소스를 만들고 **표준** 계층으로 확장합니다. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>가격 책정 계층 비교

각 가격 책정 계층에 대한 포함된 메시지 및 연결과 같은 자세한 내용은 [SignalR 서비스 가격 세부 정보를](https://azure.microsoft.com/pricing/details/signalr-service/)참조하십시오.

각 계층의 서비스 제한, 할당량 및 제약 조건 표는 [SignalR 서비스 제한을](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)참조하십시오.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 단일 SignalR 서비스 인스턴스를 확장하는 방법에 대해 배웠습니다.

확장, 샤딩 및 지역 간 시나리오에도 여러 끝점이 지원됩니다.

> [!div class="nextstepaction"]
> [여러 인스턴스를 가진 Scale SignalR 서비스](./signalr-howto-scale-multi-instances.md)
