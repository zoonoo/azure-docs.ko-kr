---
title: Azure 포털을 사용하여 Azure 외부 로드 밸런서를 다른 Azure 영역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 사용하여 외부 로드 밸런서를 한 Azure 리전에서 다른 Azure 지역으로 이동합니다.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638530"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Azure 포털을 사용하여 외부 로드 밸런서를 다른 리전으로 이동

외부 로드 밸런서를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 가진 다른 외부 로드 밸러버를 만들 수 있습니다. 재해 복구 계획의 일부로 외부 부하 분산기를 다른 지역으로 이동할 수도 있습니다.

리터럴 의미에서 Azure 외부 로드 밸런서를 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 외부 로드 밸런서의 기존 구성 및 공용 IP 주소를 내보낼 수 있습니다. 그런 다음 로드 밸런서 및 공용 IP를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 지역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다. 리소스 관리자 및 템플릿에 대한 자세한 내용은 [리소스 그룹 내보내기를 참조하여 템플릿으로](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)이동합니다.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 외부 로드 밸러버가 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 외부 로드 밸로드는 리전 간에 이동할 수 없습니다. 새 로드 밸러블러를 대상 지역의 리소스에 연결해야 합니다.

- 외부 로드 밸런서 구성을 내보내고 템플릿을 배포하여 다른 지역에서 외부 로드 밸런서를 만들려면 네트워크 기여자 역할 이상에게 할당해야 합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸런서, 네트워크 보안 그룹, 공용 IP 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 대상 지역에서 외부 로드 밸로드를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 로드 밸러버 추가를 지원할 수 있는 리소스가 충분한지 확인합니다. A[zure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.

## <a name="prepare-and-move"></a>준비 및 이동
다음 절차에서 리소스 관리자 템플릿을 사용하여 이동을 위한 외부 로드 밸런서를 준비하고 Azure 포털을 사용하여 외부 로드 밸런서 구성을 대상 영역으로 이동하는 방법을 보여 준다. 먼저 외부 로드 밸런서의 공용 IP 구성을 내보내야 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>공용 IP 템플릿을 내보내고 포털에서 공용 IP배포

1. [Azure 포털에](https://portal.azure.com) 로그인하고 **리소스 그룹을**선택합니다.
2. 원본 공용 IP가 포함된 리소스 그룹을 찾아 선택합니다.
3. **설정** > **내보내기 템플릿을 선택합니다.**
4. **내보내기 템플릿에서** **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 선택하여 온라인 편집기에서 parameters.json 파일을 엽니다.
8. 공용 IP 이름의 매개 변수를 편집하려면 소스 공용 IP 이름에서 매개 **변수** 아래의 **값** 속성을 대상 공용 IP 이름으로 변경합니다. 따옴표로 이름을 동봉합니다.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    편집기에서 **저장을** 선택합니다.

9.  **템플릿** > **편집 템플릿을** 선택하여 온라인 편집기에서 template.json 파일을 엽니다.

10. 공용 IP가 이동될 대상 영역을 편집하려면 **리소스**에서 **위치** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    지역 위치 코드를 얻으려면 [Azure 위치를](https://azure.microsoft.com/global-infrastructure/locations/)참조하십시오. 영역의 코드는 공백이 없는 지역 이름입니다. 예를 들어 미국 중부의 코드는 **중앙입니다.**
    
12. 요구 사항에 따라 원하거나 필요한 경우 템플릿의 다른 매개 변수를 변경할 수도 있습니다.

    * **SKU**. template.json 파일에서 **sku에서** **이름** 속성을 변경하여 구성에서 공용 IP의 SKU를 표준에서 기본 또는 기본에서 표준으로 변경할 수 있습니다.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        기본 및 표준 SKU 공용 IP 간의 차이점에 대한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)참조하십시오.

    * **공용 IP 할당 방법** 및 **유휴 시간 초과.** **publicIPAllocationMethod** 속성을 **동적에서** **정적** 또는 **정적에서 동적으로** 변경하여 공용 IP 할당 방법을 변경할 수 **있습니다.** 유휴 시간 초과를 원하는 값으로 **유휴 TimeoutInMinutes** 속성을 변경하여 유휴 시간 초과를 변경할 수 있습니다. 기본값은 **4입니다.**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        할당 방법 및 유휴 시간 초과 값에 대한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)참조하십시오.

 
13. 온라인 편집기에서 **저장을** 선택합니다.

14. **BASICS** > **구독을** 선택하여 대상 공용 IP가 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 선택하여 대상 공용 IP를 배포할 리소스 그룹을 선택합니다. **새 만들기를** 선택하여 대상 공용 IP에 대한 새 리소스 그룹을 만들 수 있습니다. 이름이 기존 원본 공용 IP의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **BASICS** > **위치가** 공용 IP를 배포할 대상 위치로 설정되어 있는지 확인합니다.

17. **설정에서**매개 변수 편집기에서 이전에 입력한 이름과 이름이 일치하는지 확인합니다.

18. 이용 **약관** 확인란을 선택합니다.

19. **구매를** 선택하여 대상 공용 IP를 배포합니다.

20. 이동 중인 로드 밸런서에 아웃바운드 NAT에 사용되는 다른 공용 IP가 있는 경우 이전 단계를 반복하여 두 번째 아웃바운드 공용 IP를 대상 지역으로 내보내고 배포합니다.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>외부 로드 밸러버 템플릿을 내보내고 Azure 포털에서 로드 밸러버 배포

1. [Azure 포털에](https://portal.azure.com) 로그인하고 **리소스 그룹을**선택합니다.
2. 소스 외부 로드 밸러버가 포함된 리소스 그룹을 찾아 선택합니다.
3. **설정** > **내보내기 템플릿을 선택합니다.**
4. **내보내기 템플릿에서** **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 선택하여 온라인 편집기에서 parameters.json 파일을 엽니다.

5. 외부 로드 밸러블러 이름의 매개 변수를 편집하려면 소스 외부 로드 밸러버 이름의 **값** 속성을 대상 외부 부하 분산기 의 이름으로 변경합니다. 따옴표로 이름을 동봉합니다.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  이전 단계에서 이동한 대상 공용 IP값을 편집하려면 먼저 리소스 ID를 얻은 다음 parameters.json 파일에 붙여넣아야 합니다. ID를 얻으려면 다음을 수행하십시오.

    1. 다른 브라우저 탭 또는 창에서 [Azure 포털에](https://portal.azure.com) 로그인하고 **리소스 그룹을**선택합니다.
    2. 이전 단계에서 이동한 공용 IP가 포함된 대상 리소스 그룹을 찾습니다. 이를 선택합니다.
    3. **설정** > **속성**을 선택합니다.
    4. 오른쪽 블레이드에서 **리소스 ID를** 강조 표시하고 클립보드에 복사합니다. 또는 **리소스 ID** 경로의 오른쪽에 **있는 클립보드에 복사를** 선택할 수 있습니다.
    5. 다른 브라우저 창 이나 탭에서 열려 있는 **매개 변수 편집기의** **값** 속성에 리소스 ID를 붙여넣습니다.

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. 온라인 편집기에서 **저장을** 선택합니다.


7.  로드 밸런서에 대한 아웃바운드 NAT 및 아웃바운드 규칙을 구성한 경우 아웃바운드 공용 IP의 외부 ID에 대해 이 파일에 세 번째 항목이 표시됩니다. **대상 영역에서** 이전 단계를 반복하여 아웃바운드 공용 IP에 대한 ID를 가져옵니다. 해당 ID를 parameters.json 파일에 붙여넣습니다.

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  **템플릿** > **편집 템플릿을** 선택하여 온라인 편집기에서 template.json 파일을 엽니다.
9.  외부 로드 밸러블러 구성이 이동될 대상 영역을 편집하려면 template.json 파일의 **리소스** 아래의 **위치** 속성을 변경합니다.

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. 지역 위치 코드를 얻으려면 [Azure 위치를](https://azure.microsoft.com/global-infrastructure/locations/)참조하십시오. 영역의 코드는 공백이 없는 지역 이름입니다. 예를 들어 미국 중부의 코드는 **중앙입니다.**

11. 요구 사항에 따라 원하거나 필요한 경우 템플릿의 다른 매개 변수를 변경할 수도 있습니다.

    * **SKU**. template.json 파일에서 sku 아래의 **이름** 속성을 변경하여 구성에서 기본 또는 기본에서 표준으로 외부 로드 밸러블러의 **SKU를** 변경할 수 있습니다.

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      기본 및 표준 SKU 로드 밸론서 간의 차이점에 대한 자세한 내용은 [Azure 표준 로드 밸해시 개요를](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)참조하십시오.

    * **로드 분산 규칙**. template.json 파일의 **loadBalancingRules** 섹션에 항목을 추가하거나 제거하여 구성에서 로드 분산 규칙을 추가하거나 제거할 수 있습니다.

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
       로드 분산 규칙에 대한 자세한 [내용은 Azure 로드 밸런서란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **프로브**. template.json 파일의 프로브 섹션에 항목을 추가하거나 제거하여 구성에서 로드 밸러버에 대한 **프로브를** 추가하거나 제거할 수 있습니다.

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
       자세한 내용은 [로드 밸러서 상태 프로브를](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)참조하십시오.

    * **인바운드 NAT 규칙**. template.json 파일의 **inboundNatRules** 섹션에 항목을 추가하거나 제거하여 로드 밸러터에 대한 인바운드 NAT 규칙을 추가하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료하려면 template.json 파일 끝에 **있는 형식** 속성으로 규칙을 표시하거나 제거해야 합니다.

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
        인바운드 NAT 규칙에 대한 자세한 [내용은 Azure 로드 밸러저란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **아웃바운드 규칙**. template.json 파일에서 **outboundRules** 속성을 편집하여 구성에서 아웃바운드 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         자세한 내용은 [로드 밸러서 아웃바운드 규칙을](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)참조하십시오.

12. 온라인 편집기에서 **저장을** 선택합니다.

13. **BASICS** > **구독을** 선택하여 대상 외부 로드 밸러서가 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 선택하여 대상 로드 밸러서를 배포할 리소스 그룹을 선택합니다. **새 만들기를** 선택하여 대상 외부 로드 밸러커에 대한 새 리소스 그룹을 만들 수 있습니다. 또는 Public IP에 대해 이전에 만든 기존 리소스 그룹을 선택할 수 있습니다. 이름이 기존 소스 외부 로드 밸로터의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **BASICS** > **위치가** 외부 로드 밸러저를 배포할 대상 위치로 설정되어 있는지 확인합니다.

17. **설정에서**매개 변수 편집기에서 이전에 입력한 이름과 이름이 일치하는지 확인합니다. 구성의 모든 공용 IP에 대해 리소스 IP가 채워져 있는지 확인합니다.

18. 이용 **약관** 확인란을 선택합니다.

19. **구매를** 선택하여 대상 공용 IP를 배포합니다.

## <a name="discard"></a>취소

대상 공용 IP 및 외부 로드 밸런서를 삭제하려면 해당 IP가 포함된 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택한 다음 개요 페이지 상단에서 **삭제를** 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 공용 IP 및 외부 로드 밸런서의 이동을 완료하려면 소스 공용 IP 및 외부 로드 밸런서 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 해당 리소스 그룹을 선택한 다음 각 페이지의 맨 위에 있는 **삭제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 외부 로드 밸런서를 한 리전에서 다른 리전으로 이동하고 소스 리소스를 정리했습니다. Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
