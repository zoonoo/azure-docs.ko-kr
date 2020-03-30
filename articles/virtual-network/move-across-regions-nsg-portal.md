---
title: Azure 포털을 사용하여 AZURE 네트워크 보안 그룹(NSG)을 다른 Azure 지역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 사용하여 Azure 네트워크 보안 그룹을 한 Azure 리전에서 다른 Azure 지역으로 이동합니다.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647189"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure 포털을 사용하여 AZURE 네트워크 보안 그룹(NSG)을 다른 리전으로 이동

기존 NSG를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 보안 규칙을 사용하여 NSG를 만들 수 있습니다. 재해 복구 계획의 일환으로 NSG를 다른 지역으로 이동할 수도 있습니다.

Azure 보안 그룹은 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 NSG의 기존 구성 및 보안 규칙을 내보낼 수 있습니다.  그런 다음 NSG를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 지역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다.  리소스 관리자 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure 포털을 사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 네트워크 보안 그룹이 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 네트워크 보안 그룹은 리전 간에 이동할 수 없습니다.  새 NSG를 대상 지역의 리소스에 연결해야 합니다.

- NSG 구성을 내보내고 템플릿을 배포하여 다른 리전에서 NSG를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸런서, 공용 IP 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 사용되는 대상 리전에서 NSG를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 NSG 추가를 지원할 수 있는 리소스가 충분한지 확인합니다.  A[zure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용하여 구성 및 보안 규칙이 이동하도록 네트워크 보안 그룹을 준비하고 NSG 구성 및 보안 규칙을 포털을 사용하여 대상 영역으로 이동하는 방법을 보여 주습니다.


### <a name="export-the-template-and-deploy-from-the-portal"></a>포털에서 템플릿 내보내기 및 배포

1. [Azure 포털](https://portal.azure.com) > **리소스 그룹에**로그인합니다.
2. 소스 NSG가 포함된 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > 내보내기 템플릿 을**선택합니다.**
4. **내보내기 템플릿** 블레이드에서 **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
6. NSG 이름의 매개 변수를 편집하려면 매개 변수 아래의 **값** 속성을 **변경합니다.**

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

7. 편집기의 소스 NSG 값을 대상 NSG에 대해 선택한 이름으로 변경합니다. 따옴표에 이름을 동봉해야 합니다.

8.  편집기에서 **저장을** 클릭합니다.

9.  **템플릿** > **편집 템플릿을** 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. NSG 구성 및 보안 규칙이 이동될 대상 지역을 편집하려면 온라인 편집기의 **리소스** 에서 **위치** 속성을 변경합니다.

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

11. 지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오.  지역의 코드는 공백이 없는 지역 이름, **미국** = **중부 중앙입니다.**

12. 원하는 경우 템플릿의 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

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

      대상 NSG에서 규칙 추가 또는 제거를 완료하려면 아래 예제 형식으로 **template.json** 파일 끝에 있는 사용자 지정 규칙 형식도 편집해야 합니다.

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

13. 온라인 편집기에서 **저장을** 클릭합니다.

14. **BASICS** > **구독을** 클릭하여 대상 NSG가 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 클릭하여 대상 NSG가 배포될 리소스 그룹을 선택합니다.  **새 만들기를** 클릭하여 대상 NSG에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 NSG의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **기본 확인** > **위치는** NSG를 배포할 대상 위치로 설정됩니다.

17. **설정에서** 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.

18. **이용 약관에서 확인란을**선택합니다.

19. **구매** 버튼을 클릭하여 대상 네트워크 보안 그룹을 배포합니다.

## <a name="discard"></a>취소

대상 NSG를 삭제하려면 대상 NSG가 포함된 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지 상단에서 **삭제를** 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG 이동을 완료하려면 원본 NSG 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 네트워크 보안 그룹 또는 리소스 그룹을 선택하고 각 페이지 상단에서 **삭제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
