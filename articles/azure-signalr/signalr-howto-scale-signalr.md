---
title: Azure SignalR Service 인스턴스 스케일링
description: Azure Portal 또는 Azure CLI를 통해 Azure SignalR Service 인스턴스를 스케일링하여 용량을 추가하거나 줄이는 방법을 알아봅니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 9/9/2020
ms.author: zhshang
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb01f2f96e5db4c94e759b114818360e6084255f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89595770"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Azure SignalR Service 인스턴스를 스케일링하는 방법
이 문서에서는 Azure SignalR Service 인스턴스를 스케일링하는 방법을 보여줍니다. 스케일링에는 스케일 업 및 스케일 아웃이라는 두 가지 시나리오가 있습니다.

* [스케일 업](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 더 많은 단위, 연결, 메시지 등을 가져옵니다. 가격 책정 계층을 무료에서 표준으로 변경하여 스케일 업합니다.
* [스케일 아웃](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): SignalR 단위 수를 늘립니다. 최대 100 단위까지 스케일 아웃할 수 있습니다. 단일 SignalR Service 인스턴스의 스케일링을 위해 선택할 수 있는 단위 옵션은 제한됩니다(1, 2, 5, 10, 20, 50, 100 단위).

스케일링 설정을 적용하는 데 몇 분 정도 걸립니다. 드물긴 하지만 적용하는 데 약 30분이 걸리는 경우도 있습니다. 코드를 변경하거나 서버 애플리케이션을 다시 배포할 필요는 없습니다.

개별 SignalR Service의 가격 책정 및 기능에 대한 자세한 내용은 [Azure SignalR Service 가격 정보](https://azure.microsoft.com/pricing/details/signalr-service/)를 참조하세요.  

> [!NOTE]
> SignalR Service를 **무료** 계층에서 **표준** 계층으로 변경하거나 그 반대로 변경하면 공용 서비스 IP가 변경되고, 전체 인터넷을 통해 변경 내용을 DNS 서버에 전파하는 데 일반적으로 30~60분이 걸립니다. DNS가 업데이트되기 전에는 서비스에 연결할 수 없습니다. 일반적으로 가격 책정 계층을 너무 자주 변경하지 않는 것이 좋습니다.


## <a name="scale-on-azure-portal"></a>Azure Portal에서 스케일링

1. 브라우저에서 [Azure Portal](https://portal.azure.com)을 엽니다.

2. SignalR Service 페이지의 왼쪽 메뉴에서 **스케일링** 을 선택합니다.
   
3. 해당 가격 책정 계층을 선택하고 **선택** 을 클릭합니다. **표준** 계층의 단위 수를 설정합니다.
   
    ![포털에서 스케일링](./media/signalr-howto-scale/signalr-howto-scale.png)

4. **저장** 을 클릭합니다.

## <a name="scale-using-azure-cli"></a>Azure CLI를 사용한 크기 조정

이 스크립트는 **무료** 계층의 새 SignalR Service 리소스와 새 리소스 그룹을 만들고 **표준** 계층까지 스케일링합니다. 

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

포함된 메시지 및 연결과 같은 각 가격 책정 계층에 대한 자세한 내용은 [SignalR Service 가격 정보](https://azure.microsoft.com/pricing/details/signalr-service/)를 참조하세요.

각 계층의 서비스 한도, 할당량, 제약 조건 표는 [SignalR Service 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 단일 SignalR Service 인스턴스를 스케일링하는 방법을 배웠습니다.

스케일링, 분할, 지역 간 시나리오를 위해 여러 엔드포인트도 지원됩니다.

> [!div class="nextstepaction"]
> [여러 인스턴스를 사용하여 SignalR Service 스케일링](./signalr-howto-scale-multi-instances.md)
