---
title: Logic Apps REST API를 사용 하 여 통합 서비스 환경 (ISEs) 만들기
description: 에서 Vnet (Azure virtual network)에 액세스할 수 있도록 Logic Apps REST API를 사용 하 여 ISE (통합 서비스 환경)를 만듭니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478839"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Logic Apps를 사용 하 여 ISE (통합 서비스 환경)를 만듭니다 REST API

이 문서에서는 논리 앱 및 통합 계정에 [Azure virtual network](../virtual-network/virtual-networks-overview.md)에 대 한 액세스 권한이 필요한 시나리오에 대 한 Logic Apps REST API 통해 [ISE ( *통합 서비스 환경* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) )를 만드는 방법을 보여 줍니다. ISE는 전용 저장소 및 "글로벌" 다중 테 넌 트 Logic Apps 서비스와 별도로 유지 되는 기타 리소스를 사용 하는 격리 된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. 또한 ISE는 사용자 고유의 고정 IP 주소를 제공 합니다. 이러한 IP 주소는 공용 다중 테 넌 트 서비스의 논리 앱에서 공유 하는 고정 IP 주소와는 별개입니다.

[샘플 Azure Resource Manager 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) 또는 [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)를 사용 하 여 ISE를 만들 수도 있습니다.

> [!IMPORTANT]
> ISE에서 실행 되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 요금제와 다른 가격 책정 계획을 사용 합니다. ISEs에 대 한 가격 책정 및 청구의 작동 방식에 대 한 자세한 [Logic Apps 내용은 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* Azure Portal ISE를 만들 때 [ise에 대 한 액세스를 가능 하 게 하](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) 는 동일한 [필수 구성 요소](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 요구 사항

* HTTPS PUT 요청을 사용 하 여 Logic Apps REST API를 호출 하 여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman](https://www.getpostman.com/downloads/)을 사용 하거나이 작업을 수행 하는 논리 앱을 빌드할 수 있습니다.

## <a name="send-the-request"></a>요청 보내기

Logic Apps REST API 호출 하 여 ISE를 만들려면 HTTPS PUT 요청을 만듭니다.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 버전을 사용 하려면 ISE 커넥터에 대 한 HTTP PUT 요청을 직접 수행 해야 합니다.

배포는 일반적으로 완료 하는 데 2 시간 이내에 수행 됩니다. 경우에 따라 배포에 최대 4 시간이 걸릴 수 있습니다. 배포 상태를 확인 하려면 [Azure Portal](https://portal.azure.com)의 Azure 도구 모음에서 알림 아이콘을 선택 합니다. 그러면 알림 창이 열립니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용 하기 전에 기다려야 한다는 것을 의미 합니다.
>
> 가상 네트워크를 삭제 하는 경우 Azure는 일반적으로 서브넷을 해제 하기 전까지 최대 2 시간이 걸리지만이 작업은 더 오래 걸릴 수 있습니다. 
> 가상 네트워크를 삭제 하는 경우 아직 연결 된 리소스가 없는지 확인 합니다. 
> [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조 하세요.

## <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함 합니다.

* `Content-type`:이 속성 값을로 `application/json`설정 합니다.

* `Authorization`: 사용 하려는 Azure 구독 또는 리소스 그룹에 대 한 액세스 권한이 있는 고객에 대 한 전달자 토큰으로이 속성 값을 설정 합니다.

### <a name="request-body-syntax"></a>요청 본문 구문

다음은 ISE를 만들 때 사용할 속성을 설명 하는 요청 본문 구문입니다.

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

이 예제 요청 본문은 샘플 값을 보여 줍니다.

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

