---
title: Azure 포털을 사용하여 Azure 내부 로드 밸러터를 다른 Azure 영역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 사용하여 Azure 내부 로드 밸러터를 한 Azure 리전에서 다른 Azure 지역으로 이동
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638811"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 내부 로드 밸러터를 다른 지역으로 이동

기존 내부 로드 밸러버를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 가진 내부 부하 분산기를 만들 수 있습니다. 재해 복구 계획의 일부로 내부 부하 분산기를 다른 지역으로 이동할 수도 있습니다.

Azure 내부 로드 밸로드는 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 내부 부하 분산 검사기의 기존 구성 및 가상 네트워크를 내보낼 수 있습니다.  그런 다음 로드 밸러블러 및 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 영역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다.  리소스 관리자 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure 포털을 사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 내부 로드 밸러버가 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 내부 로드 밸로드는 리전 간에 이동할 수 없습니다.  새 로드 밸러블러를 대상 지역의 리소스에 연결해야 합니다.

- 내부 로드 밸런서 구성을 내보내고 템플릿을 배포하여 다른 지역에서 내부 로드 밸런서를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸러버, 네트워크 보안 그룹, 가상 시스템 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 사용되는 대상 지역에서 내부 로드 밸로드워서를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 로드 밸로드밸로드 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) 참조


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용하여 이동을 위해 내부 로드 밸런서를 준비하고 내부 로드 밸런서 구성을 Azure 포털을 사용하여 대상 영역으로 이동하는 방법을 보여 주습니다.  이 프로세스의 일부로 내부 로드 밸러블러의 가상 네트워크 구성을 포함해야 하며 먼저 내부 로드 밸러블러를 이동하기 전에 수행해야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Azure 포털에서 가상 네트워크 템플릿 을 내보내고 배포

1. [Azure 포털](https://portal.azure.com) > **리소스 그룹에**로그인합니다.
2. 원본 가상 네트워크가 포함된 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > 내보내기 템플릿 을**선택합니다.**
4. **내보내기 템플릿** 블레이드에서 **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
6. 가상 네트워크 이름의 매개 변수를 편집하려면 매개 변수 아래의 **값** 속성을 **변경합니다.**

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
7. 편집기의 소스 가상 네트워크 이름 값을 대상 VNET에 대해 선택한 이름으로 변경합니다. 따옴표에 이름을 동봉해야 합니다.

8. 편집기에서 **저장을** 클릭합니다.

9. **템플릿** > **편집 템플릿을** 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. VNET이 이동할 대상 영역을 편집하려면 리소스 에서 **위치** 속성을 변경합니다.

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

11. 지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오.  지역의 코드는 공백이 없는 지역 이름, **미국** = **중부 중앙입니다.**

12. 원하는 경우 **template.json** 파일에서 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

    * **주소 공간** - **리소스** > **addressSpace** 섹션을 수정하고 **template.json** 파일에서 **addressPrefixes** 속성을 변경하여 저장하기 전에 VNET의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷** - **template.json** 파일의 **서브넷** 섹션을 수정하여 서브넷 이름과 서브넷 주소 공간을 변경하거나 추가할 수 있습니다. 서브넷의 이름은 **name** 속성을 변경하여 변경할 수 있습니다. 서브넷 주소 공간은 **template.json** 파일에서 **addressPrefix** 속성을 변경하여 변경할 수 있습니다.

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

         **template.json** 파일에서 주소 접두사를 변경하려면 위에 나열된 섹션과 아래에 나열된 **유형** 섹션의 두 위치에서 편집해야 합니다.  위의 것과 일치하도록 **addressPrefix** 속성을 변경합니다.

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

13. 온라인 편집기에서 **저장을** 클릭합니다.

14. **BASICS** > **구독을** 클릭하여 대상 VNET이 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 클릭하여 대상 VNET이 배포될 리소스 그룹을 선택합니다.  **새 만들기를** 클릭하여 대상 VNET에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 VNET의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **기본** > 확인**위치는** VNET을 배포할 대상 위치로 설정됩니다.

17. **설정에서** 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.

18. **이용 약관에서 확인란을**선택합니다.

19. **구매** 버튼을 클릭하여 대상 가상 네트워크를 배포합니다.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>내부 로드 밸러블러 템플릿 내보내기 및 Azure PowerShell에서 배포

1. [Azure 포털](https://portal.azure.com) > **리소스 그룹에**로그인합니다.
2. 소스 내부 부하 분산이 포함된 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > 내보내기 템플릿 을**선택합니다.**
4. **내보내기 템플릿** 블레이드에서 **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.

6. 내부 로드 밸러블러 이름의 매개 변수를 편집하려면 소스 내부 로드 밸러터 이름의 속성 **defaultValue를** 대상 내부 부하 밸로터의 이름으로 변경하여 이름이 따옴표에 있는지 확인합니다.

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

6. 위에 이동된 대상 가상 네트워크의 값을 편집하려면 먼저 리소스 ID를 얻은 다음 **복사하여 parameters.json** 파일에 붙여넣아야 합니다. ID를 얻으려면 다음을 수행하십시오.

    1. 다른 브라우저 탭 또는 창에서 [Azure 포털](https://portal.azure.com) > **리소스 그룹에** 로그인합니다.
    2. 위의 단계에서 이동된 가상 네트워크가 포함된 대상 리소스 그룹을 찾아 클릭합니다.
    3. > **설정** > **속성**선택 .
    4. 오른쪽 블레이드에서 **리소스 ID를** 강조 표시하고 클립보드에 복사합니다.  또는 **리소스 ID** 경로 의 오른쪽에 있는 클립보드 단추복사본을 **클릭할** 수 있습니다.
    5. 리소스 ID를 **defaultValue** 속성에 다른 브라우저 창 또는 탭에서 열리는 **매개 변수 편집기** 편집기에 붙여넣습니다.

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
    6. 온라인 편집기에서 **저장을** 클릭합니다.

7. **템플릿** > **편집 템플릿을** 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.
8. 내부 로드 밸러블러 구성이 이동될 대상 영역을 편집하려면 **template.json** 파일의 **리소스** 아래의 **위치** 속성을 변경합니다.

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

9.  지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오.  지역의 코드는 공백이 없는 지역 이름, **미국** = **중부 중앙입니다.**

10. 원하는 경우 템플릿의 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

    * **Sku** - **template.json** 파일에서 sku**이름** 속성을 변경하여 구성에서 기본 또는 기본으로 **구성의 sku를** > 변경할 수 있습니다.

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
      기본 및 표준 sku 로드 밸로드 밸로드밸로드 의 차이점에 대한 자세한 내용은 [Azure 표준 로드 밸로드 밸로드서 개요를](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 참조하십시오.

    * **로드 균형 조정 규칙** - **template.json** 파일의 **loadBalancingRules** 섹션에 항목을 추가하거나 제거하여 구성에서 부하 분산 규칙을 추가하거나 제거할 수 있습니다.

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
       로드 분산 규칙에 대한 자세한 내용은 [Azure 로드 밸런서란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **프로브** - **template.json** 파일의 프로브 섹션에 항목을 추가하거나 제거하여 구성에서 로드 밸러버에 대한 **프로브를** 추가하거나 제거할 수 있습니다.

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
       Azure 로드 밸러서 상태 프로브에 대한 자세한 내용은 [로드 밸론저 상태 프로브를 참조하십시오.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **인바운드 NAT 규칙** - **template.json** 파일의 **인바운드NatRules** 섹션에 항목을 추가하거나 제거하여 로드 밸러터에 대한 인바운드 NAT 규칙을 추가하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료하려면 **template.json** 파일 끝에 **있는 형식** 속성으로 규칙을 표시하거나 제거해야 합니다.

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
        인바운드 NAT 규칙에 대한 자세한 내용은 [Azure 로드 밸러저란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. 온라인 편집기에서 **저장을** 클릭합니다.

13. **BASICS** > **구독을** 클릭하여 대상 내부 로드 밸러서가 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 클릭하여 대상 로드 밸러서가 배포될 리소스 그룹을 선택합니다.  **새 만들기를** 클릭하여 대상 내부 부하 분산 검사기의 새 리소스 그룹을 만들거나 가상 네트워크에 대해 위에서 만든 기존 리소스 그룹을 선택할 수 있습니다.  이름이 기존 소스 내부 부하 분산기의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **BASICS** > **위치** 확인은 내부 부하 분산 장치를 배포할 대상 위치로 설정됩니다.

17. **설정에서** 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.  리소스 인덱스가 구성의 모든 가상 네트워크에 대해 채워지는지 확인합니다.

18. **이용 약관에서 확인란을**선택합니다.

19. **구매** 버튼을 클릭하여 대상 가상 네트워크를 배포합니다.

## <a name="discard"></a>취소

대상 가상 네트워크 및 내부 로드 밸러버를 폐기하려면 대상 가상 네트워크 및 내부 로드 밸러버가 포함된 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지 상단에서 **삭제를** 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 및 내부 로드 밸러버의 이동을 완료하려면 소스 가상 네트워크 및 내부 로드 밸러버 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 가상 네트워크 및 내부 로드 밸런서 또는 리소스 그룹을 선택하고 각 페이지 상단에서 **삭제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 내부 로드 밸런서를 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
