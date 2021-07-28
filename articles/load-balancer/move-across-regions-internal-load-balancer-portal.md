---
title: Azure Portal을 사용하여 Azure 내부 부하 분산 장치를 다른 Azure 지역으로 이동
description: Azure Portal을 사용하여 Azure 내부 부하 분산 장치를 Azure 지역 간에 이동하려면 Azure Resource Manager 템플릿을 사용
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698493"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 내부 부하 분산 장치를 다른 지역으로 이동

기존 Azure 내부 부하 분산 장치를 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성의 내부 부하 분산 장치를 만드는 것이 좋습니다. 또한, 재해 복구 계획의 일부로 내부 부하 분산 장치를 다른 지역으로 이동하는 것이 좋습니다.

Azure 내부 부하 분산 장치는 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 내부 부하 분산 장치의 기존 구성과 가상 네트워크를 내보낼 수 있습니다.  그런 다음, 내부 부하 분산 장치와 가상 네트워크를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 스테이징할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 이동하려는 Azure 지역에 Azure 내부 부하 분산 장치가 있는지 확인합니다.

- Azure 내부 부하 분산 장치는 지역 간에 이동할 수 없습니다.  새 부하 분산 장치를 대상 지역의 리소스에 연결해야 합니다.

- 내부 부하 분산 장치 구성을 내보내고 템플릿을 배포하여 다른 지역에 내부 부하 분산 장치를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃은 부하 분산 장치, 네트워크 보안 그룹, 가상 머신, 가상 네트워크를 포함하되 이에 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 Azure 지역에 내부 부하 분산 장치를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 부하 분산 장치 추가를 지원할 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 내부 부하 분산 장치 이동을 준비하고 Azure Portal을 사용하여 내부 부하 분산 장치 구성을 대상 지역으로 이동하는 방법을 보여 줍니다.  이 프로세스의 일부로 내부 부하 분산 장치를 이동하기 전에 내부 부하 분산 장치의 가상 네트워크 구성이 포함되어야 하고 먼저 수행되어야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>가상 네트워크 템플릿을 내보내고 Azure Portal에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인합니다.
2. 원본 가상 네트워크를 포함하는 리소스 그룹을 찾아 클릭합니다.
3. **설정** > **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
6. 가상 네트워크 이름의 매개 변수를 편집하려면 **매개 변수** 아래의 **값** 속성을 변경합니다.

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
7. 편집기의 원본 가상 네트워크 이름 값을 대상 VNET에 대해 선택한 이름으로 변경합니다. 이름을 따옴표로 묶어야 합니다.

8. 편집기에서 **저장** 을 클릭합니다.

9. **템플릿** > **템플릿 편집** 을 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. VNET을 이동할 대상 영역을 편집하려면 리소스 아래의 **위치** 속성을 변경합니다.

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

11. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.  지역 코드는 공백이 없는 지역 이름입니다(예: **Central US** = **centralus**).

12. 또한 선택하는 경우 **template.json** 파일의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택할 수 있습니다.

    * **주소 공간** - 저장하기 전에 **리소스** > **addressSpace** 섹션을 수정하고 **template.json** 파일에서 **addressPrefixes** 속성을 변경하여 VNET의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷** - **template.json** 파일의 **서브넷** 섹션을 수정하여 서브넷 이름과 서브넷 주소 공간을 변경하거나 추가할 수 있습니다. 서브넷의 이름은 **이름** 속성을 변경하여 바꿀 수 있습니다. 서브넷 주소 공간은 **template.json** 파일에서 **addressPrefix** 속성을 변경하여 바꿀 수 있습니다.

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

         **template.json** 파일에서 주소 접두사를 변경하려면 두 곳(위에 나열된 섹션과 아래에 나열된 **형식** 섹션)을 편집해야 합니다.  **addressPrefix** 속성을 위의 속성과 일치하도록 변경합니다.

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

13. 온라인 편집기에서 **저장** 을 클릭합니다.

14. **기본** > **구독** 을 클릭하여 대상 VNET을 배포할 구독을 선택합니다.

15. **기본** > **리소스 그룹** 을 클릭하여 대상 VNET을 배포할 리소스 그룹을 선택합니다.  **새로 만들기** 를 클릭하여 대상 VNET에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 VNET의 원본 리소스 그룹과 동일하지 않은지 확인합니다.

16. **기본** > **위치** 가 VNET을 배포하려는 대상 위치로 설정되어 있는지 확인합니다.

17. **설정** 에서 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.

18. **사용 약관** 아래의 확인란을 선택합니다.

19. **구매** 단추를 클릭하여 대상 가상 네트워크를 배포합니다.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>내부 부하 분산 장치 템플릿을 내보내고 Azure PowerShell에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인합니다.
2. 원본 내부 부하 분산 장치를 포함하는 리소스 그룹을 찾아 클릭합니다.
3. **설정** > **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.

6. 내부 부하 분산 장치 이름의 매개 변수를 편집하려면 원본 내부 부하 분산 장치 이름의 **defaultValue** 속성을 대상 내부 부하 분산 장치 이름으로 변경하고 이름을 따옴표로 묶었는지 확인합니다.

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. 위에서 이동한 대상 가상 네트워크의 값을 편집하려면 먼저 리소스 ID를 얻은 다음 **parameters.json** 파일에 복사하여 붙여넣어야 합니다. 추적 ID를 확인하려면 다음 단계를 따르세요.

    1. 다른 브라우저 탭 또는 창에서 [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인합니다.
    2. 위의 단계에서 이동한 가상 네트워크를 포함하는 대상 리소스 그룹을 찾아 클릭합니다.
    3. **설정** > **속성** 을 선택합니다.
    4. 오른쪽 블레이드에서 **리소스 ID** 를 강조 표시하고 클립보드에 복사합니다.  또는 **리소스 ID** 경로 오른쪽의 **클립보드로 복사** 단추를 클릭할 수 있습니다.
    5. 다른 브라우저 창이나 탭에서 열려 있는 **매개 변수 편집** 편집기에서 **defaultValue** 속성에 리소스 ID를 붙여넣습니다.

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. 온라인 편집기에서 **저장** 을 클릭합니다.

7. **템플릿** > **템플릿 편집** 을 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.
8. 내부 부하 분산 장치 구성이 이동할 대상 지역을 편집하려면 **template.json** 파일에 있는 **리소스** 아래의 **위치** 속성을 변경합니다.

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.  지역 코드는 공백이 없는 지역 이름입니다(예: **Central US** = **centralus**).

10. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **SKU** - **template.json** 파일에서 **SKU** > **이름** 속성을 변경하여 구성의 내부 부하 분산 장치 SKU를 표준에서 기본으로 또는 기본에서 표준으로 변경할 수 있습니다.

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      기본 및 표준 SKU 부하 분산 장치 간의 차이점에 대한 자세한 내용은 [Azure 표준 Load Balancer 개요](./load-balancer-overview.md)를 참조하세요.

    * **부하 분산 규칙** - **template.json** 파일의 **loadBalancingRules** 섹션에 항목을 추가하거나 제거하여 구성에서 부하 분산 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       부하 분산 규칙에 대한 자세한 내용은 [Azure Load Balancer란?](./load-balancer-overview.md) 페이지를 참조하세요.

    * **프로브** - **template.json** 파일의 **프로브** 섹션에 항목을 추가하거나 제거하여 구성의 부하 분산 장치에 대한 프로브를 추가하거나 제거할 수 있습니다.

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure Load Balancer 상태 프로브에 대한 자세한 내용은 [Load Balancer 상태 프로브](./load-balancer-custom-probe-overview.md)를 참조하세요.

    * **인바운드 NAT 규칙** - **template.json** 파일의 **inboundNatRules** 섹션에 항목을 추가하거나 제거하여 부하 분산 장치에 대한 인바운드 NAT 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        인바운드 NAT 규칙의 추가 또는 제거를 완료하려면 **template.json** 파일 끝에 **유형** 속성으로 규칙이 표시되거나 제거되어야 합니다.

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        인바운드 NAT 규칙에 대한 자세한 내용은 [Azure Load Balancer란?](./load-balancer-overview.md)을 참조하세요.

12. 온라인 편집기에서 **저장** 을 클릭합니다.

13. **기본** > **구독** 을 클릭하여 대상 내부 부하 분산 장치를 배포할 구독을 선택합니다.

15. **기본** > **리소스 그룹** 을 클릭하여 대상 부하 분산 장치를 배포할 리소스 그룹을 선택합니다.  **새로 만들기** 를 클릭하여 대상 내부 부하 분산 장치에 대한 새 리소스 그룹을 만들거나 가상 네트워크에 대해 위에서 만든 기존 리소스 그룹을 선택할 수 있습니다.  이름이 기존의 원본 내부 부하 분산 장치의 원본 리소스 그룹과 동일하지 않은지 확인합니다.

16. **기본** > **위치** 가 내부 부하 분산 장치를 배포하려는 대상 위치로 설정되어 있는지 확인합니다.

17. **설정** 에서 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.  구성의 모든 가상 네트워크에 대해 리소스 ID가 채워졌는지 확인합니다.

18. **사용 약관** 아래의 확인란을 선택합니다.

19. **구매** 단추를 클릭하여 대상 가상 네트워크를 배포합니다.

## <a name="discard"></a>취소

대상 가상 네트워크 및 내부 부하 분산 장치를 삭제하려는 경우 대상 가상 네트워크 및 내부 부하 분산 장치를 포함하는 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지의 맨 위에서 **삭제** 를 선택합니다.

## <a name="clean-up"></a>정리

변경 사항을 커밋하고 가상 네트워크와 내부 부하 분산 장치의 이동을 완료하려면 원본 가상 네트워크 및 내부 부하 분산 장치 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 가상 네트워크 및 내부 부하 분산 장치 또는 리소스 그룹을 선택하고 각 페이지의 위쪽에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 내부 부하 분산 장치를 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)