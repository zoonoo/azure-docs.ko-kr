---
title: Azure Portal를 사용 하 여 Azure 외부 부하 분산 장치를 다른 Azure 지역으로 이동
description: Azure Portal를 사용 하 여 한 Azure 지역에서 다른 Azure 지역으로 외부 부하 분산 장치를 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693752"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 외부 부하 분산 장치를 다른 지역으로 이동

외부 부하 분산 장치를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트에 대해 동일한 구성으로 다른 외부 부하 분산 장치를 만들 수 있습니다. 재해 복구 계획의 일부로 외부 부하 분산 장치를 다른 지역으로 이동할 수도 있습니다.

리터럴 점에서 Azure 외부 부하 분산 장치를 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 외부 부하 분산 장치의 기존 구성 및 공용 IP 주소를 내보낼 수 있습니다. 그런 다음 부하 분산 장치 및 공용 IP를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다. 리소스 관리자 및 템플릿에 대 한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조 하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 외부 부하 분산 장치가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 외부 부하 분산 장치는 지역 간에 이동할 수 없습니다. 새 부하 분산 장치를 대상 지역의 리소스에 연결 해야 합니다.

- 외부 부하 분산 장치 구성을 내보내고 템플릿을 배포 하 여 다른 지역에 외부 부하 분산 장치를 만들려면 네트워크 참가자 역할이 이상 할당 되어 있어야 합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, 네트워크 보안 그룹, 공용 Ip, 가상 네트워크 등이 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 대상 지역에 외부 부하 분산 장치를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 부하 분산 장치 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.

## <a name="prepare-and-move"></a>준비 및 이동
다음 절차에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 외부 부하 분산 장치를 준비 하 고 Azure Portal를 사용 하 여 외부 부하 분산 장치 구성을 대상 지역으로 이동 하는 방법을 보여 줍니다. 먼저 외부 부하 분산 장치의 공용 IP 구성을 내보내야 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>공용 IP 템플릿을 내보내고 포털에서 공용 IP를 배포 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.
2. 원본 공용 IP를 포함 하는 리소스 그룹을 찾아 선택 합니다.
3. **설정**  >  **템플릿 내보내기** 를 선택 합니다.
4. **템플릿 내보내기** 에서 **배포** 를 선택 합니다.
5. **템플릿**  >  **매개 변수 편집** 을 선택 하 여 온라인 편집기에서 파일에 parameters.js를 엽니다.
8. 공용 IP 이름의 매개 변수를 편집 하려면 원본 공용 ip 이름에서 **매개 변수** 아래의 **value** 속성을 대상 공용 ip의 이름으로 변경 합니다. 이름을 따옴표로 묶습니다.

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

    편집기에서 **저장** 을 선택 합니다.

9.  **템플릿**  >  **템플릿 편집** 을 선택 하 여 온라인 편집기에서 파일에 template.js를 엽니다.

10. 공용 IP가 이동 될 대상 영역을 편집 하려면 **리소스** 에서 **위치** 속성을 변경 합니다.

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
  
    지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백 없이 지역 이름입니다. 예를 들어 미국 중부에 대 한 코드는 **centralus** 입니다.
    
12. 요구 사항에 따라 필요에 따라 원할 경우 템플릿에서 다른 매개 변수를 변경할 수도 있습니다.

    * **SKU**. 파일의 template.js에 있는 **sku** 에서 **이름** 속성을 변경 하 여 구성의 공용 IP에 대 한 sku를 표준에서 기본 또는 기본에서 표준으로 변경할 수 있습니다.

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

        기본 및 표준 SKU 공용 ip 간의 차이점에 대 한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제](../virtual-network/virtual-network-public-ip-address.md)를 참조 하세요.

    * **공용 IP 할당 방법** 및 **유휴 제한 시간** 입니다. **PublicIPAllocationMethod** 속성을 **동적** 에서 **정적** 으로 또는 **정적** 에서 **동적** 으로 변경 하 여 공용 IP 할당 방법을 변경할 수 있습니다. **IdleTimeoutInMinutes** 속성을 원하는 값으로 변경 하 여 유휴 시간 제한을 변경할 수 있습니다. 기본값은 **4** 입니다.

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

        할당 방법 및 유휴 시간 제한 값에 대 한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제](../virtual-network/virtual-network-public-ip-address.md)를 참조 하세요.

 
13. 온라인 편집기에서 **저장** 을 선택 합니다.

14. **기본**  >  **구독** 을 선택 하 여 대상 공용 IP를 배포할 구독을 선택 합니다.

15. **기본**  >  **리소스 그룹** 을 선택 하 여 대상 공용 IP가 배포 될 리소스 그룹을 선택 합니다. **새로 만들기** 를 선택 하 여 대상 공용 IP에 대 한 새 리소스 그룹을 만들 수 있습니다. 이름이 기존 원본 공용 IP의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본**  >  **위치가** 공용 IP를 배포 하려는 대상 위치로 설정 되어 있는지 확인 합니다.

17. **설정** 아래에서 이름이 매개 변수 편집기에서 이전에 입력 한 이름과 일치 하는지 확인 합니다.

18. 사용 **약관** 확인란을 선택 합니다.

19. **구매** 를 선택 하 여 대상 공용 IP를 배포 합니다.

20. 이동 중인 부하 분산 장치에 대해 아웃 바운드 NAT에 사용 되는 다른 공용 IP가 있는 경우 이전 단계를 반복 하 여 두 번째 아웃 바운드 공용 IP를 대상 지역으로 내보내고 배포 합니다.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>외부 부하 분산 장치 템플릿을 내보내고 Azure Portal에서 부하 분산 장치를 배포 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.
2. 원본 외부 부하 분산 장치를 포함 하는 리소스 그룹을 찾아 선택 합니다.
3. **설정**  >  **템플릿 내보내기** 를 선택 합니다.
4. **템플릿 내보내기** 에서 **배포** 를 선택 합니다.
5. **템플릿**  >  **매개 변수 편집** 을 선택 하 여 온라인 편집기에서 파일에 parameters.js를 엽니다.

5. 외부 부하 분산 장치 이름의 매개 변수를 편집 하려면 원본 외부 부하 분산 장치 이름의 **value** 속성을 대상 외부 부하 분산 장치의 이름으로 변경 합니다. 이름을 따옴표로 묶습니다.

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

6.  이전 단계에서 이동한 대상 공용 IP의 값을 편집 하려면 먼저 리소스 ID를 가져온 후 파일의 parameters.js에 붙여넣어야 합니다. ID를 가져오려면 다음을 수행 합니다.

    1. 다른 브라우저 탭 또는 창에서 [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **리소스 그룹** 을 선택 합니다.
    2. 이전 단계에서 이동한 공용 IP를 포함 하는 대상 리소스 그룹을 찾습니다. 이 폴더를 선택합니다.
    3. **설정** > **속성** 을 선택합니다.
    4. 오른쪽 블레이드에서 **리소스 ID** 를 강조 표시 하 고 클립보드에 복사 합니다. 또는 **리소스 ID** 경로 오른쪽의 **클립보드로 복사를** 선택할 수 있습니다.
    5. 다른 브라우저 창이 나 탭에서 열려 있는 **매개 변수 편집** 편집기에서 **값** 속성에 리소스 ID를 붙여넣습니다.

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
    6. 온라인 편집기에서 **저장** 을 선택 합니다.


7.  부하 분산 장치에 대 한 아웃 바운드 NAT 및 아웃 바운드 규칙을 구성한 경우이 파일에 아웃 바운드 공용 IP의 외부 ID에 대 한 세 번째 항목이 표시 됩니다. **대상 지역** 에서 위의 단계를 반복 하 여 아웃 바운드 공용 IP에 대 한 ID를 가져옵니다. 해당 ID를 파일의 parameters.js에 붙여넣습니다.

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

8.  **템플릿**  >  **템플릿 편집** 을 선택 하 여 온라인 편집기에서 파일에 template.js를 엽니다.
9.  외부 부하 분산 장치 구성을 이동할 대상 지역을 편집 하려면 파일의 template.js에 있는 **리소스** 에서 **위치** 속성을 변경 합니다.

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

10. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백 없이 지역 이름입니다. 예를 들어 미국 중부에 대 한 코드는 **centralus** 입니다.

11. 요구 사항에 따라 필요에 따라 원할 경우 템플릿에서 다른 매개 변수를 변경할 수도 있습니다.

    * **SKU**. 파일의 template.js에서 **SKU** 아래의 **이름** 속성을 변경 하 여 구성의 외부 부하 분산 장치에 대 한 sku를 표준에서 기본 또는 기본에서 표준으로 변경할 수 있습니다.

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
      기본 및 표준 SKU 부하 분산 장치 간의 차이점에 대 한 자세한 내용은 [Azure 표준 Load Balancer 개요](./load-balancer-overview.md)를 참조 하세요.

    * **부하 분산 규칙**. template.js파일의 **loadBalancingRules** 섹션에서 항목을 추가 하거나 제거 하 여 구성에서 부하 분산 규칙을 추가 하거나 제거할 수 있습니다.

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
       부하 분산 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)을 참조 하세요.

    * **프로브**. 파일에서 template.js의 **프로브** 섹션에서 항목을 추가 하거나 제거 하 여 구성에서 부하 분산 장치에 대 한 프로브를 추가 하거나 제거할 수 있습니다.

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
       자세한 내용은 [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)를 참조 하세요.

    * **인바운드 NAT 규칙**. template.js파일의 **loadbalancer.inboundnatrules** 섹션에서 항목을 추가 하거나 제거 하 여 부하 분산 장치에 대 한 인바운드 NAT 규칙을 추가 하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료 하려면 다음 파일에서 template.js끝에 **유형** 속성으로 해당 규칙을 제거 해야 합니다.

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
        인바운드 NAT 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)을 참조 하세요.

    * **아웃 바운드 규칙**. 파일의 template.js에서 **outboundRules** 속성을 편집 하 여 구성에서 아웃 바운드 규칙을 추가 하거나 제거할 수 있습니다.

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

         자세한 내용은 [Load Balancer 아웃 바운드 규칙](./load-balancer-outbound-connections.md#outboundrules)을 참조 하세요.

12. 온라인 편집기에서 **저장** 을 선택 합니다.

13. **기본**  >  **구독** 을 선택 하 여 대상 외부 부하 분산 장치를 배포할 구독을 선택 합니다.

15. **기본**  >  **리소스 그룹** 을 선택 하 여 대상 부하 분산 장치가 배포 될 리소스 그룹을 선택 합니다. **새로 만들기** 를 선택 하 여 대상 외부 부하 분산 장치에 대 한 새 리소스 그룹을 만들 수 있습니다. 또는 공용 IP에 대해 이전에 만든 기존 리소스 그룹을 선택할 수 있습니다. 이름이 기존 원본 외부 부하 분산 장치의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본**  >  **위치가** 외부 부하 분산 장치를 배포 하려는 대상 위치로 설정 되어 있는지 확인 합니다.

17. **설정** 아래에서 이름이 매개 변수 편집기의 앞에서 입력 한 이름과 일치 하는지 확인 합니다. 구성의 모든 공용 Ip에 대해 리소스 Id가 채워졌는지 확인 합니다.

18. 사용 **약관** 확인란을 선택 합니다.

19. **구매** 를 선택 하 여 대상 공용 IP를 배포 합니다.

## <a name="discard"></a>취소

대상 공용 IP 및 외부 부하 분산 장치를 삭제 하려면 해당 항목을 포함 하는 리소스 그룹을 삭제 합니다. 이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택한 다음 개요 페이지의 위쪽에서 **삭제** 를 선택 합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 공용 IP 및 외부 부하 분산 장치의 이동을 완료 하려면 원본 공용 IP 및 외부 부하 분산 장치 또는 리소스 그룹을 삭제 합니다. 이렇게 하려면 포털의 대시보드에서 해당 리소스 그룹을 선택 하 고 각 페이지 위쪽에서 **삭제** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 외부 부하 분산 장치를 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다. Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)