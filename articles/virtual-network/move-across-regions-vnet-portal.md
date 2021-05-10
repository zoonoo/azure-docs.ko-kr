---
title: Azure Portal을 사용하여 Azure 가상 네트워크를 다른 Azure 지역으로 이동합니다.
description: Resource Manager 템플릿 및 Azure Portal을 사용하여 Azure 지역 간에 Azure 가상 네트워크를 이동합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223311"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 가상 네트워크를 Azure 지역으로 이동

기존 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동하는 다양한 시나리오가 있습니다. 예를 들어, 기존 가상 네트워크와 동일한 테스트용 구성 및 가용성을 사용해서 가상 네트워크를 만들 수 있습니다. 또는 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동할 수 있습니다.

Azure Resource Manager 템플릿을 사용하여 가상 네트워크를 다른 지역으로 이동할 수 있습니다. 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음, 새 지역에 템플릿을 배포하여 이 작업을 수행합니다. Resource Manager 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- 가상 네트워크가 이동을 시작하려는 Azure 지역에 있는지 확인합니다.

- 가상 네트워크를 내보내고 템플릿을 배포하여 다른 지역에 가상 네트워크를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 가상 네트워크 피어링은 다시 생성되지 않으며 템플릿에 여전히 있는 경우 실패합니다. 템플릿을 내보내기 전에 가상 네트워크 피어를 모두 제거해야 합니다. 그런 다음, 가상 네트워크 이동 후 다시 설정할 수 있습니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 배치에는 부하 분산 장치, NSG(네트워크 보안 그룹) 및 공용 IP를 포함하되 이에 국한되지 않습니다.

- Azure 구독에서 대상 지역에 가상 네트워크를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 가상 네트워크 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-for-the-move"></a>이동 준비
이 섹션에서는 Resource Manager 템플릿을 사용하여 가상 네트워크의 이동 준비를 수행합니다. 그런 다음, Azure Portal을 사용하여 가상 네트워크를 대상 지역으로 이동합니다.

Azure Portal을 사용하여 가상 네트워크를 내보내고 대상 가상 네트워크를 배포하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, **리소스 그룹** 을 선택합니다.
1. 원본 가상 네트워크를 포함하는 리소스 그룹을 찾아 선택합니다.
1. **설정** > **템플릿 내보내기** 를 선택합니다.
1. **템플릿 내보내기** 창에서 **배포** 를 선택합니다.
1. 온라인 편집기에서 *parameters.json* 파일을 열려면 **템플릿** > **매개 변수 편집** 을 선택합니다.
1. 가상 네트워크 이름의 매개 변수를 편집하려면 **parameters** 에서 **value** 속성을 변경합니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. 편집기에서 원본 가상 네트워크 이름 값을 대상 가상 네트워크에 사용할 이름으로 변경합니다. 이름을 큰따옴표로 묶어야 합니다.

1. 편집기에서 **저장** 을 선택합니다.

1. 온라인 편집기에서 *template.json* 파일을 열려면 **템플릿** > **템플릿 편집** 을 선택합니다.

1. 온라인 편집기에서 가상 네트워크가 이동될 대상 지역을 편집하려면 **resources** 에서 **location** 속성을 변경합니다.

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요. 지역에 대한 코드는 공백 없는 지역 이름입니다(예: **미국 중부** = **centralus**).

1. (선택 사항) 또한 요구 사항에 따라 템플릿의 다른 매개 변수를 변경할 수 있습니다.

    * **주소 공간**: 파일을 저장하기 전에 **resources** > **addressSpace** 섹션을 수정하고 **addressPrefixes** 속성을 변경하여 가상 네트워크의 주소 공간을 변경할 수 있습니다.

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **서브넷**: 템플릿의 **subnets** 섹션을 변경하여 서브넷 이름 및 서브넷 주소 공간을 변경하거나 추가할 수 있습니다. **name** 속성을 변경하여 서브넷의 이름을 변경할 수 있습니다. 또한 **addressPrefix** 속성을 변경하여 서브넷 주소 공간을 변경할 수 있습니다.

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        *template.json* 파일의 주소 접두사를 변경하려면 이전 섹션의 코드와 다음 코드의 **type** 섹션에서 편집합니다. 다음 코드의 **addressPrefix** 속성을 이전 섹션의 코드에 있는 **addressPrefix** 속성과 일치하도록 변경합니다.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. 온라인 편집기에서 **저장** 을 선택합니다.

1. 대상 가상 네트워크를 배포할 구독을 선택하려면 **기본 사항** > **구독** 을 선택합니다.

1. 대상 가상 네트워크를 배포할 리소스 그룹을 선택하려면 **기본 사항** > **리소스 그룹** 을 선택합니다. 

    대상 가상 네트워크에 대한 새 리소스 그룹을 만들어야 하는 경우 **새로 만들기** 를 선택합니다. 이름이 기존 가상 네트워크의 원본 리소스 그룹 이름과 같지 않은지 확인합니다.

1. **기본 사항** > **위치** 가 가상 네트워크를 배포하려는 대상 위치로 설정되어 있는지 확인합니다.

1. **설정** 아래에서 이름이 매개 변수 편집기에서 이전에 입력한 이름과 일치하는지 확인합니다.

1. **사용 약관** 확인란을 선택합니다.

1. 대상 가상 네트워크를 배포하려면 **구매** 를 선택합니다.

## <a name="delete-the-target-virtual-network"></a>대상 가상 네트워크 삭제

대상 가상 네트워크를 삭제하려면 대상 가상 네트워크를 포함하는 리소스 그룹을 삭제합니다. 이를 수행하려면:
1. Azure Portal 대시보드에서 리소스 그룹을 선택합니다.
1. **개요** 창 맨 위에서 **삭제** 를 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크의 이동을 완료하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제합니다. 이를 수행하려면:
1. Azure Portal 대시보드에서 가상 네트워크 또는 리소스 그룹을 선택합니다.
1. 각 창 맨 위에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동한 다음, 불필요한 원본 리소스를 정리했습니다. Azure에서 지역 간 리소스 이동 및 재해 복구에 대한 자세한 내용은 다음을 참조하세요.


- [리소스를 새 리소스 그룹 또는 구독으로 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure 가상 머신 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)