---
title: Azure 포털을 사용하여 Azure 가상 네트워크를 다른 Azure 지역으로 이동합니다.
description: 리소스 관리자 템플릿 및 Azure 포털을 사용하여 Azure 가상 네트워크를 한 Azure 리전에서 다른 Azure 로 이동합니다.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640791"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 가상 네트워크를 다른 리전으로 이동

기존 Azure 가상 네트워크를 한 리전에서 다른 리전으로 이동하기 위한 다양한 시나리오가 있습니다. 예를 들어 기존 가상 네트워크와 테스트 및 가용성을 테스트하기 위해 동일한 구성을 가진 가상 네트워크를 만들 수 있습니다. 또는 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동할 수 있습니다.

Azure 리소스 관리자 템플릿을 사용하여 가상 네트워크를 다른 지역으로 이동할 수 있습니다. 이렇게 하려면 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 지역에 배포합니다. 리소스 관리자 템플릿에 대한 자세한 내용은 빠른 시작: Azure 포털 을 [사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 가상 네트워크가 이동할 Azure 지역에 있는지 확인합니다.

- 가상 네트워크를 내보내고 템플릿을 배포하여 다른 지역에서 가상 네트워크를 만들려면 네트워크 기여자 역할 이상이 있어야 합니다.

- 가상 네트워크 피어링은 다시 생성되지 않으며 템플릿에 여전히 있으면 실패합니다. 템플릿을 내보내기 전에 가상 네트워크 피어를 제거해야 합니다. 그런 다음 가상 네트워크 이동 후 다시 설정할 수 있습니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸런서, NSG(네트워크 보안 그룹) 및 공용 IP가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 대상 리전에서 가상 네트워크를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원팀에 문의하십시오.

- 구독에 이 프로세스에 대한 가상 네트워크 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-for-the-move"></a>이동 준비
이 섹션에서는 리소스 관리자 템플릿을 사용하여 이동을 위한 가상 네트워크를 준비합니다. 그런 다음 Azure 포털을 사용하여 가상 네트워크를 대상 지역으로 이동합니다.

Azure 포털을 사용하여 가상 네트워크를 내보내고 대상 가상 네트워크를 배포하려면 다음을 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인한 다음 **리소스 그룹을 선택합니다.**
1. 원본 가상 네트워크가 포함된 리소스 그룹을 찾은 다음 선택합니다.
1. **설정** > **내보내기 템플릿을 선택합니다.**
1. **내보내기 템플릿** 창에서 **배포를**선택합니다.
1. 온라인 편집기에서 *parameters.json* 파일을 열려면 **템플릿** > **편집 매개 변수를 선택합니다.**
1. 가상 네트워크 이름의 매개 변수를 편집하려면 매개 변수 아래의 **값** 속성을 **변경합니다.**

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

1. 편집기에서 편집기의 소스 가상 네트워크 이름 값을 대상 가상 네트워크에 대해 원하는 이름으로 변경합니다. 따옴표로 이름을 동봉해야 합니다.

1. 편집기에서 **저장을** 선택합니다.

1. 온라인 편집기에서 *template.json* 파일을 열려면 **템플릿** > **편집 템플릿을**선택합니다.

1. 온라인 편집기에서 가상 네트워크가 이동될 대상 영역을 편집하려면 **리소스**아래의 **위치** 속성을 변경합니다.

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

1. 지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오. 리전의 코드는 공백이 없는 지역 이름입니다(예: **미국** = **중부 중심).**

1. (선택 사항) 요구 사항에 따라 템플릿의 다른 매개 변수를 변경할 수도 있습니다.

    * **주소 공간**: 파일을 저장하기 전에 **리소스** > **addressSpace** 섹션을 수정하고 **addressPrefixes** 속성을 변경하여 가상 네트워크의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷**: 템플릿의 서브넷 섹션을 변경하여 서브넷 이름과 서브넷 주소 공간을 변경하거나 추가할 수 **있습니다.** **이름** 속성을 변경하여 서브넷의 이름을 변경할 수 있습니다. 그리고 **주소Prefix** 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 있습니다.

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

        *template.json* 파일에서 주소 접두사를 변경하려면 이전 섹션의 코드와 다음 코드의 **형식** 섹션에서 두 위치에서 편집합니다. 다음 코드에서 **addressPrefix** 속성을 변경하여 이전 섹션의 코드에서 **addressPrefix** 속성과 일치합니다.

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

1. 온라인 편집기에서 **저장을**선택합니다.

1. 대상 가상 네트워크를 배포할 구독을 선택하려면 기본**구독을** **선택합니다.** > 

1. 대상 가상 네트워크를 배포할 리소스 그룹을 선택하려면 기본**리소스 그룹을** **선택합니다.** >  

    대상 가상 네트워크에 대한 새 리소스 그룹을 만들어야 하는 경우 새 가상 네트워크 **만들기를 선택합니다.** 이름이 기존 가상 네트워크의 소스 리소스 그룹 이름과 같지 않은지 확인합니다.

1. **기본** > **위치가** 가상 네트워크를 배포할 대상 위치로 설정되어 있는지 확인합니다.

1. **설정에서**매개 변수 편집기에서 이전에 입력한 이름과 이름이 일치하는지 확인합니다.

1. 이용 **약관** 확인란을 선택합니다.

1. 대상 가상 네트워크를 배포하려면 **구입을**선택합니다.

## <a name="delete-the-target-virtual-network"></a>대상 가상 네트워크 삭제

대상 가상 네트워크를 삭제하려면 대상 가상 네트워크가 포함된 리소스 그룹을 삭제합니다. 이렇게 하려면 다음을 수행합니다.
1. Azure 포털 대시보드에서 리소스 그룹을 선택합니다.
1. **개요** 창 상단에서 **삭제를**선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 이동을 완료하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 다음을 수행합니다.
1. Azure 포털 대시보드에서 가상 네트워크 또는 리소스 그룹을 선택합니다.
1. 각 창 의 맨 위에서 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 포털을 사용하여 Azure 가상 네트워크를 한 리전에서 다른 리전으로 이동한 다음 불필요한 소스 리소스를 정리했습니다. Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure 가상 컴퓨터를 다른 지역으로 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
