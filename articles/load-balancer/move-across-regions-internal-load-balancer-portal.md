---
title: Azure Portal를 사용 하 여 Azure 내부 Load Balancer을 다른 Azure 지역으로 이동
description: Azure Portal를 사용 하 여 azure 지역 간에 Azure 내부 Load Balancer를 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 88aedb97f659725887026d0c83be88cbde27ae4f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219668"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 내부 Load Balancer을 다른 지역으로 이동

기존 내부 부하 분산 장치를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트에 대해 동일한 구성을 사용 하 여 내부 부하 분산 장치를 만들 수 있습니다. 재해 복구 계획의 일부로 내부 부하 분산 장치를 다른 지역으로 이동할 수도 있습니다.

Azure 내부 부하 분산 장치는 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 내부 부하 분산 장치의 기존 구성과 가상 네트워크를 내보낼 수 있습니다.  그런 다음 부하 분산 장치 및 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다.  리소스 관리자 및 템플릿에 [대 한 자세한 내용은 빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 내부 부하 분산 장치가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 내부 부하 분산 장치는 지역 간에 이동할 수 없습니다.  새 부하 분산 장치를 대상 지역의 리소스에 연결 해야 합니다.

- 내부 부하 분산 장치 구성을 내보내고 템플릿을 배포 하 여 다른 지역에 내부 부하 분산 장치를 만들려면 네트워크 참가자 역할이 이상 필요 합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, 네트워크 보안 그룹, 가상 머신 및 가상 네트워크만 포함 되지만이에 국한 되지는 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에 내부 부하 분산 장치를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 부하 분산 장치 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 내부 부하 분산 장치를 준비 하 고 Azure Portal를 사용 하 여 내부 부하 분산 장치 구성을 대상 지역으로 이동 하는 방법을 보여 줍니다.  이 프로세스의 일부로 내부 부하 분산 장치의 가상 네트워크 구성이 포함 되어야 하 고 내부 부하 분산 장치를 이동 하기 전에 먼저 수행 되어야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>가상 네트워크 템플릿을 내보내고 Azure Portal에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹**에 로그인 합니다.
2. 원본 가상 네트워크를 포함 하는 리소스 그룹을 찾아 클릭 합니다.
3. > **설정** > **템플릿 내보내기**를 선택 합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택 합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭 하 여 온라인 편집기에서 **parameters. json** 파일을 엽니다.
6. 가상 네트워크 이름의 매개 변수를 편집 하려면 **매개 변수**에서 **value** 속성을 변경 합니다.

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
7. 편집기의 원본 가상 네트워크 이름 값을 대상 VNET에 대해 선택한 이름으로 변경 합니다. 이름을 따옴표로 묶어야 합니다.

8. 편집기에서 **저장** 을 클릭 합니다.

9. **템플릿** > **템플릿 편집** 을 클릭 하 여 온라인 편집기에서 **템플릿 json** 파일을 엽니다.

10. VNET이 이동 될 대상 지역을 편집 하려면 리소스에서 **위치** 속성을 변경 합니다.

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

11. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.  영역에 대 한 코드는 공백 없이 **미국** = 중부**centralus**지역 이름입니다.

12. 또한 사용자가 선택 하는 경우 **템플릿 json** 파일의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **주소 공간** - **리소스** > **addressSpace** 섹션을 수정 하 고 **템플릿. json** 파일에서 **addressPrefixes** 속성을 변경 하 여 저장 하기 전에 VNET의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷** - **템플릿. json** 파일의 **서브넷** 섹션을 수정 하 여 서브넷 이름 및 서브넷 주소 공간을 변경 하거나에 추가할 수 있습니다. **이름** 속성을 변경 하 여 서브넷의 이름을 변경할 수 있습니다. **AddressPrefix** 파일에서 다음 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 **있습니다.**

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

         **템플릿 json** 파일에서 주소 접두사를 변경 하려면 위에 나열 된 섹션 및 아래에 나열 된 **형식** 섹션에서 두 위치를 편집 해야 합니다.  **AddressPrefix** 속성을 위와 일치 하도록 변경 합니다.

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

13. 온라인 편집기에서 **저장** 을 클릭 합니다.

14. **기본** > **구독** 을 클릭 하 여 대상 VNET을 배포할 구독을 선택 합니다.

15. **기본** > **리소스 그룹** 을 클릭 하 여 대상 VNET을 배포할 리소스 그룹을 선택 합니다.  **새로 만들기** 를 클릭 하 여 대상 VNET에 대 한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 VNET의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본** > **위치가** VNET을 배포할 대상 위치로 설정 되어 있는지 확인 합니다.

17. 위의 매개 변수 편집기에서 입력 한 이름과 이름이 일치 하는 **설정** 아래를 확인 합니다.

18. 사용 **약관**아래 상자를 선택 합니다.

19. **구매** 단추를 클릭 하 여 대상 가상 네트워크를 배포 합니다.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>내부 부하 분산 장치 템플릿을 내보내고 Azure PowerShell에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹**에 로그인 합니다.
2. 원본 내부 부하 분산 장치를 포함 하는 리소스 그룹을 찾아 클릭 합니다.
3. > **설정** > **템플릿 내보내기**를 선택 합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택 합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭 하 여 온라인 편집기에서 **parameters. json** 파일을 엽니다.

6. 내부 부하 분산 장치 이름의 매개 변수를 편집 하려면 원본 내부 부하 분산 장치 이름의 속성 **defaultValue** 를 대상 내부 부하 분산 장치의 이름으로 변경 하 고 이름이 따옴표 안에 있는지 확인 합니다.

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

6. 위에서 이동한 대상 가상 네트워크의 값을 편집 하려면 먼저 리소스 ID를 가져온 다음 **매개 변수. json** 파일에 복사 하 여 붙여넣어야 합니다. ID를 가져오려면 다음을 수행 합니다.

    1. 다른 브라우저 탭 또는 창에서 [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인 합니다.
    2. 위의 단계에서 이동 된 가상 네트워크를 포함 하는 대상 리소스 그룹을 찾아 클릭 합니다.
    3. > **설정** > **속성**을 선택 합니다.
    4. 오른쪽 블레이드에서 **리소스 ID** 를 강조 표시 하 고 클립보드에 복사 합니다.  또는 **리소스 ID** 경로 오른쪽의 **클립보드로 복사** 단추를 클릭할 수 있습니다.
    5. **DefaultValue** 속성에 리소스 ID를 다른 브라우저 창이 나 탭에서 연 **매개 변수 편집** 편집기에 붙여넣습니다.

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
    6. 온라인 편집기에서 **저장** 을 클릭 합니다.

7. **템플릿** > **템플릿 편집** 을 클릭 하 여 온라인 편집기에서 **템플릿 json** 파일을 엽니다.
8. 내부 부하 분산 장치 구성을 이동할 대상 지역을 편집 하려면 **템플릿. json** 파일의 **리소스** 에서 **위치** 속성을 변경 합니다.

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

9.  지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.  영역에 대 한 코드는 공백 없이 **미국** = 중부**centralus**지역 이름입니다.

10. 또한 선택 하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택 사항입니다.

    * **Sku** - **템플릿의** **sku** > **이름** 속성을 변경 하 여 구성의 내부 부하 분산 장치에 대 한 sku를 standard에서 basic 또는 basic에서 standard로 변경할 수 있습니다.

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
      기본 및 표준 sku 부하 분산 장치 간의 차이점에 대 한 자세한 내용은 [Azure 표준 Load Balancer 개요](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 를 참조 하세요.

    * **부하 분산 규칙** - **템플릿. Json** 파일의 **loadBalancingRules** 섹션에 항목을 추가 하거나 제거 하 여 구성에서 부하 분산 규칙을 추가 하거나 제거할 수 있습니다.

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
       부하 분산 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 을 참조 하세요.

    * **프로브** - **템플릿. json** 파일의 **프로브** 섹션에 항목을 추가 하거나 제거 하 여 구성의 부하 분산 장치에 대 한 프로브를 추가 하거나 제거할 수 있습니다.

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
       Azure Load Balancer 상태 프로브에 대 한 자세한 내용은 [상태 프로브 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) 를 참조 하세요.

    * **인바운드 nat 규칙** - **템플릿. Json** 파일의 **loadbalancer.inboundnatrules** 섹션에 항목을 추가 하거나 제거 하 여 부하 분산 장치에 대 한 인바운드 nat 규칙을 추가 하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료 하려면 **템플릿 json** 파일의 끝에 **유형** 속성으로 규칙을 표시 하거나 제거 해야 합니다.

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
        인바운드 NAT 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 을 참조 하세요.

12. 온라인 편집기에서 **저장** 을 클릭 합니다.

13. **기본** > **구독** 을 클릭 하 여 대상 내부 부하 분산 장치를 배포할 구독을 선택 합니다.

15. **기본** > **리소스 그룹** 을 클릭 하 여 대상 부하 분산 장치가 배포 될 리소스 그룹을 선택 합니다.  **새로 만들기** 를 클릭 하 여 대상 내부 부하 분산 장치에 대 한 새 리소스 그룹을 만들거나 가상 네트워크에 대해 위에서 만든 기존 리소스 그룹을 선택할 수 있습니다.  이름이 기존 원본 내부 부하 분산 장치의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본** > **위치가** 내부 부하 분산 장치를 배포 하려는 대상 위치로 설정 되어 있는지 확인 합니다.

17. 위의 매개 변수 편집기에서 입력 한 이름과 이름이 일치 하는 **설정** 아래를 확인 합니다.  구성의 가상 네트워크에 대 한 리소스 Id가 채워졌는지 확인 합니다.

18. 사용 **약관**아래 상자를 선택 합니다.

19. **구매** 단추를 클릭 하 여 대상 가상 네트워크를 배포 합니다.

## <a name="discard"></a>삭제

대상 가상 네트워크 및 내부 부하 분산 장치를 삭제 하려는 경우 대상 가상 네트워크 및 내부 부하 분산 장치를 포함 하는 리소스 그룹을 삭제 합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택 하 고 개요 페이지의 위쪽에서 **삭제** 를 선택 합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 및 내부 부하 분산 장치의 이동을 완료 하려면 원본 가상 네트워크 및 내부 부하 분산 장치 또는 리소스 그룹을 삭제 합니다. 이렇게 하려면 포털의 대시보드에서 가상 네트워크 및 내부 부하 분산 장치 또는 리소스 그룹을 선택 하 고 각 페이지의 위쪽에서 **삭제** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 한 지역에서 다른 지역으로 Azure 내부 부하 분산 장치를 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
