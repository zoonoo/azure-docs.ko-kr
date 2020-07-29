---
title: Azure Portal를 사용 하 여 Azure 가상 네트워크를 다른 Azure 지역으로 이동 합니다.
description: 리소스 관리자 템플릿 및 Azure Portal를 사용 하 여 azure 지역 간에 Azure 가상 네트워크를 이동 합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: ed3da649ba65484a79b42ba5bb45431839e123d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711445"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 가상 네트워크를 다른 지역으로 이동

기존 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동 하는 다양 한 시나리오가 있습니다. 예를 들어 기존 가상 네트워크로 테스트 및 가용성에 대해 동일한 구성으로 가상 네트워크를 만들 수 있습니다. 또는 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동 하는 것이 좋습니다.

Azure Resource Manager 템플릿을 사용 하 여 가상 네트워크를 다른 지역으로 이동 하는 것을 완료할 수 있습니다. 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여이 작업을 수행 합니다. 리소스 관리자 템플릿에 대 한 자세한 내용은 빠른 시작 [: Azure Portal를 사용 하 여 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)를 참조 하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 가상 네트워크가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- 가상 네트워크를 내보내고 템플릿을 배포 하 여 다른 지역에서 가상 네트워크를 만들려면 네트워크 참가자 역할이 이상 있어야 합니다.

- 가상 네트워크 피어 링은 다시 생성 되지 않으며 템플릿에 여전히 있는 경우 실패 합니다. 템플릿을 내보내기 전에 가상 네트워크 피어를 모두 제거 해야 합니다. 그런 다음 가상 네트워크 이동 후 다시 설정할 수 있습니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, NSGs (네트워크 보안 그룹) 및 공용 Ip가 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 대상 지역에서 가상 네트워크를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용 하도록 설정 하려면 지원 담당자에 게 문의 하세요.

- 구독에이 프로세스에 대 한 가상 네트워크 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조 하세요.


## <a name="prepare-for-the-move"></a>이동 준비
이 섹션에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 가상 네트워크를 준비 합니다. 그런 다음 Azure Portal를 사용 하 여 가상 네트워크를 대상 지역으로 이동 합니다.

Azure Portal를 사용 하 여 가상 네트워크를 내보내고 대상 가상 네트워크를 배포 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 **리소스 그룹**을 선택 합니다.
1. 원본 가상 네트워크를 포함 하는 리소스 그룹을 찾아 선택 합니다.
1. **설정**  >  **템플릿 내보내기**를 선택 합니다.
1. **템플릿 내보내기** 창에서 **배포**를 선택 합니다.
1. 온라인 편집기에서 파일 *에parameters.js* 를 열려면 **템플릿**  >  **매개 변수 편집**을 선택 합니다.
1. 가상 네트워크 이름의 매개 변수를 편집 하려면 **매개 변수**에서 **value** 속성을 변경 합니다.

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

1. 편집기에서 대상 가상 네트워크에 사용할 이름으로 편집기의 원본 가상 네트워크 이름 값을 변경 합니다. 이름을 따옴표로 묶어야 합니다.

1. 편집기에서 **저장** 을 선택 합니다.

1. 온라인 편집기에서 파일 *에template.js* 를 열려면 **템플릿**  >  **템플릿 편집**을 선택 합니다.

1. 온라인 편집기에서 가상 네트워크가 이동 될 대상 지역을 편집 하려면 **리소스**에서 **위치** 속성을 변경 합니다.

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

1. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요. 영역에 대 한 코드는 공백 없이 지역 이름입니다 (예: **미국 중부**  =  **centralus**).

1. 필드 요구 사항에 따라 템플릿에서 다른 매개 변수를 변경할 수도 있습니다.

    * **주소 공간**: 파일을 저장 하기 전에 **resources**  >  **addressSpace** 섹션을 수정 하 고 **addressPrefixes** 속성을 변경 하 여 가상 네트워크의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷**: 템플릿의 **서브넷** 섹션을 변경 하 여 서브넷 이름 및 서브넷 주소 공간을 변경 하거나 추가할 수 있습니다. **이름** 속성을 변경 하 여 서브넷의 이름을 변경할 수 있습니다. **AddressPrefix** 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 있습니다.

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

        *template.js* 에서 파일의 주소 접두사를 변경 하려면 이전 섹션의 코드와 다음 코드의 **형식** 섹션에서 두 위치를 편집 합니다. 이전 섹션에 있는 코드의 **addressPrefix** 속성과 일치 하도록 다음 코드의 **addressPrefix** 속성을 변경 합니다.

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

1. 온라인 편집기에서 **저장**을 선택 합니다.

1. 대상 가상 네트워크를 배포할 구독을 선택 하려면 **기본**  >  **구독**을 선택 합니다.

1. 대상 가상 네트워크가 배포 될 리소스 그룹을 선택 하려면 **기본**  >  **리소스 그룹**을 선택 합니다. 

    대상 가상 네트워크에 대 한 새 리소스 그룹을 만들어야 하는 경우 **새로 만들기**를 선택 합니다. 이름이 기존 가상 네트워크의 원본 리소스 그룹 이름과 같지 않은지 확인 합니다.

1. **기본**  >  **위치가** 가상 네트워크를 배포 하려는 대상 위치로 설정 되어 있는지 확인 합니다.

1. **설정**아래에서 이름이 매개 변수 편집기에서 이전에 입력 한 이름과 일치 하는지 확인 합니다.

1. 사용 **약관** 확인란을 선택 합니다.

1. 대상 가상 네트워크를 배포 하려면 **구매**를 선택 합니다.

## <a name="delete-the-target-virtual-network"></a>대상 가상 네트워크를 삭제 합니다.

대상 가상 네트워크를 삭제 하려면 대상 가상 네트워크를 포함 하는 리소스 그룹을 삭제 합니다. 이를 수행하려면:
1. Azure Portal 대시보드에서 리소스 그룹을 선택 합니다.
1. **개요** 창 위쪽에서 **삭제**를 선택 합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 이동을 완료 하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제 합니다. 이를 수행하려면:
1. Azure Portal 대시보드에서 가상 네트워크 또는 리소스 그룹을 선택 합니다.
1. 각 창의 위쪽에서 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal를 사용 하 여 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동한 다음 불필요 한 소스 리소스를 정리 했습니다. Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure 가상 머신 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
