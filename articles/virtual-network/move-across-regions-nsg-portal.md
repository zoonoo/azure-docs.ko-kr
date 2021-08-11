---
title: Azure Portal을 사용하여 Azure NSG(네트워크 보안 그룹)를 다른 Azure 지역으로 이동
description: Azure Portal을 사용하여 Azure 네트워크 보안 그룹을 한 Azure 지역에서 다른 Azure 지역으로 이동하려면 Azure Resource Manager 템플릿을 사용합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217280"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure NSG(네트워크 보안 그룹)를 다른 지역으로 이동

기존 NSG를 한 지역에서 다른 지역으로 이동하는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 보안 규칙을 사용하여 NSG를 만들 수 있습니다. 또한 재해 복구 계획의 일환으로 NSG를 다른 지역으로 이동할 수도 있습니다.

Azure 보안 그룹은 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 NSG의 기존 구성 및 보안 규칙을 내보낼 수 있습니다.  그런 다음, NSG를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 스테이징할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 이동할 Azure 지역에 Azure 네트워크 보안 그룹이 있는지 확인합니다.

- Azure 네트워크 보안 그룹은 지역 간에 이동할 수 없습니다.  새 NSG를 대상 지역의 리소스에 연결해야 합니다.

- NSG 구성을 내보내고 템플릿을 배포하여 다른 지역에 NSG를 만들려면 네트워크 참가자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃은 부하 분산 장치, 공용 IP 및 가상 네트워크를 포함하되 이에 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 지역에 NSG를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 NSG 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 구성 및 보안 규칙 이동을 위해 네트워크 보안 그룹을 준비하고 포털을 사용하여 NSG 구성 및 보안 규칙을 대상 지역으로 이동하는 방법을 보여줍니다.


### <a name="export-the-template-and-deploy-from-the-portal"></a>템플릿 내보내기 및 포털에서 배포

1. [Azure Portal](https://portal.azure.com) > **리소스 그룹** 에 로그인합니다.
2. 원본 NSG를 포함하는 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > **템플릿 내보내기** 를 선택합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택합니다.
5. **템플릿** > **매개 변수 편집** 을 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
6. NSG 이름의 매개 변수를 편집하려면 **매개 변수** 아래의 **값** 속성을 변경합니다.

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. 편집기의 원본 NSG 값을 대상 NSG에 대해 선택한 이름으로 변경합니다. 이름을 따옴표로 묶어야 합니다.

8.  편집기에서 **저장** 을 클릭합니다.

9.  **템플릿** > **템플릿 편집** 을 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. NSG 구성 및 보안 규칙이 이동될 대상 지역을 편집하려면 온라인 편집기의 **리소스** 에서 **location** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.  지역 코드는 공백이 없는 지역 이름인 **미국 중부** = **centralus** 입니다.

12. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **보안 규칙** - **template.json** 파일의 **securityRules** 섹션에 규칙을 추가하거나 제거하여 대상 NSG에 배포되는 규칙을 편집할 수 있습니다.

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      대상 NSG에서 규칙의 추가 또는 제거를 완료하려면 다음과 같은 형식으로 **template.json** 파일의 끝에 있는 사용자 지정 규칙 형식도 편집해야 합니다.

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. 온라인 편집기에서 **저장** 을 클릭합니다.

14. **기본** > **구독** 을 클릭하여 대상 NSG를 배포할 구독을 선택합니다.

15. **기본** > **리소스 그룹** 을 클릭하여 대상 NSG를 배포할 리소스 그룹을 선택합니다.  **새로 만들기** 를 클릭하여 대상 NSG에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 NSG의 원본 리소스 그룹과 동일하지 않은지 확인합니다.

16. **기본** > **위치** 가 NSG를 배포할 대상 위치로 설정되어 있는지 확인합니다.

17. **설정** 에서 이름이 위의 매개 변수 편집기에서 입력한 이름과 일치하는지 확인합니다.

18. **계약조건** 아래의 상자를 선택합니다.

19. **구매** 단추를 클릭하여 대상 네트워크 보안 그룹을 배포합니다.

## <a name="discard"></a>취소

대상 NSG를 버리려면 대상 NSG를 포함하는 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지 위쪽에서 **삭제** 를 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료하려면 원보 NSG 또는 리소스 그룹을 삭제합니다. 이 작업을 수행하려면 포털의 대시보드에서 네트워크 보안 그룹 또는 리소스 그룹을 선택하고 각 페이지의 위쪽에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)