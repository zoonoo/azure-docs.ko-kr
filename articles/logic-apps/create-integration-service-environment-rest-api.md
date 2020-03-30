---
title: 로직 앱 REST API를 사용하여 통합 서비스 환경(ISEs) 생성
description: Azure 논리 앱에서 Azure 가상 네트워크(VNET)에 액세스할 수 있도록 논리 앱 REST API를 사용하여 통합 서비스 환경(ISE)을 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127664"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>논리 앱 REST API를 사용하여 통합 서비스 환경(ISE) 만들기

이 문서에서는 논리 앱 및 통합 계정이 [Azure 가상 네트워크에](../virtual-network/virtual-networks-overview.md)액세스해야 하는 시나리오에 대해 Logic Apps REST API를 통해 [ISE(통합 *서비스 환경)를* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 만드는 방법을 보여 줍니다. ISE는 전용 저장소 및 "글로벌" 다중 테넌트 논리 앱 서비스와 별도로 유지되는 기타 리소스를 사용하는 격리된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. ISE는 또한 사용자 고유의 정적 IP 주소를 제공합니다. 이러한 IP 주소는 공용 다중 테넌트 서비스의 논리 앱에서 공유하는 정적 IP 주소와 는 별개입니다.

대신 Azure 포털을 사용하여 ISE를 만들려면 [Azure 논리 앱에서 Azure 가상 네트워크에 연결 을](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)참조하십시오.

> [!IMPORTANT]
> ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 가격 책정 계획과 다른 가격 책정 계획을 사용합니다. ISEs의 가격 책정 및 청구 작동 방식은 [논리 앱 가격 책정 모델을](../logic-apps/logic-apps-pricing.md#fixed-pricing)참조하십시오. 가격 책정 요금은 [논리 앱 가격 책정을](../logic-apps/logic-apps-pricing.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 포털에서 ISE를 만들 때와 동일한 [필수 구성 조건](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 요구 [사항으로 ISE에 대한 액세스를 사용하도록 설정합니다.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* HTTPS PUT 요청으로 논리 앱 REST API를 호출하여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman을](https://www.getpostman.com/downloads/)사용하거나 이 작업을 수행하는 논리 앱을 빌드할 수 있습니다.

## <a name="send-the-request"></a>요청 보내기

논리 앱 REST API를 호출하여 ISE를 만들려면 다음 HTTPS PUT 요청을 수행하십시오.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 로직 앱 REST API 2019-05-01 버전은 ISE 커넥터에 대한 HTTP PUT 요청을 직접 만들어야 합니다.

배포를 완료하는 데 일반적으로 2시간 이내걸립니다. 경우에 따라 배포에 최대 4시간이 걸릴 수 있습니다. Azure [포털](https://portal.azure.com)에서 배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 기다려야 할 수 있음을 의미합니다.
>
> 가상 네트워크를 삭제하는 경우 Azure는 일반적으로 서브넷을 해제하기까지 최대 2시간이 걸리지만 이 작업은 더 오래 걸릴 수 있습니다. 
> 가상 네트워크를 삭제할 때는 여전히 연결된 리소스가 없는지 확인합니다. 
> [가상 네트워크 삭제를](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)참조하십시오.

## <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함합니다.

* `Content-type`: 이 속성 `application/json`값을 로 설정합니다.

* `Authorization`: 사용하려는 Azure 구독 또는 리소스 그룹에 액세스할 수 있는 고객에 대해 이 속성 값을 보유자 토큰으로 설정합니다.

### <a name="request-body-syntax"></a>요청 본문 구문

ISE를 만들 때 사용할 속성을 설명하는 요청 본문 구문은 다음과 같습니다.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>요청 본문 예제

이 예제 요청 본문에는 샘플 값이 표시됩니다.

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

