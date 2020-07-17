---
title: Azure Portal를 사용 하 여 Azure NSG (네트워크 보안 그룹)를 다른 Azure 지역으로 이동
description: Azure Portal을 사용 하 여 azure 네트워크 보안 그룹을 한 Azure 지역에서 다른 지역으로 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: a22dc6dc0c4fc199d3f262b18aeeae5090a06dce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84689319"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure NSG (네트워크 보안 그룹)를 다른 지역으로 이동

한 지역에서 다른 지역으로 기존 NSGs를 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트를 위한 동일한 구성 및 보안 규칙을 사용 하 여 NSG를 만들 수 있습니다. 재해 복구 계획의 일환으로 NSG를 다른 지역으로 이동할 수도 있습니다.

Azure 보안 그룹은 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 NSG의 기존 구성 및 보안 규칙을 내보낼 수 있습니다.  그런 다음 NSG를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure 네트워크 보안 그룹이 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 네트워크 보안 그룹은 지역 간에 이동할 수 없습니다.  새 NSG를 대상 지역의 리소스에 연결 해야 합니다.

- NSG 구성을 내보내고 템플릿을 배포 하 여 다른 지역에서 NSG를 만들려면 네트워크 참가자 역할 이상이 필요 합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, 공용 Ip, 가상 네트워크 등이 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에서 NSGs를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 NSGs 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 구성 및 보안 규칙 이동에 대 한 네트워크 보안 그룹을 준비 하 고, 포털을 사용 하 여 NSG 구성 및 보안 규칙을 대상 지역으로 이동 하는 방법을 보여 줍니다.


### <a name="export-the-template-and-deploy-from-the-portal"></a>템플릿을 내보내고 포털에서 배포

1. [Azure Portal](https://portal.azure.com)  >  **리소스 그룹**에 로그인 합니다.
2. 원본 NSG를 포함 하는 리소스 그룹을 찾아 클릭 합니다.
3. > **설정**  >  **템플릿 내보내기**를 선택 합니다.
4. **템플릿 내보내기** 블레이드에서 **배포** 를 선택 합니다.
5. **템플릿**  >  **매개 변수 편집** 을 클릭 하 여 온라인 편집기에서 파일 **에parameters.js** 를 엽니다.
6. NSG 이름의 매개 변수를 편집 하려면 **매개 변수**에서 **value** 속성을 변경 합니다.

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

7. 편집기의 원본 NSG 값을 대상 NSG에 대해 선택한 이름으로 변경 합니다. 이름을 따옴표로 묶어야 합니다.

8.  편집기에서 **저장** 을 클릭 합니다.

9.  **템플릿**  >  **템플릿 편집** 을 클릭 하 여 온라인 편집기에서 파일 **에template.js** 를 엽니다.

10. NSG 구성 및 보안 규칙이 이동 될 대상 지역을 편집 하려면 온라인 편집기의 **리소스** 에서 **위치** 속성을 변경 합니다.

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

11. 지역 위치 코드를 가져오려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.  영역에 대 한 코드는 공백 없이 **미국 중부**  =  **centralus**지역 이름입니다.

12. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **보안 규칙** - **template.js** 파일의 **securityrules** 섹션에 규칙을 추가 하거나 제거 하 여 대상 nsg에 배포 되는 규칙을 편집할 수 있습니다.

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

      대상 NSG에서 규칙의 추가 또는 제거를 완료 하려면 아래 예제 형식으로 파일 **에 대 한template.js** 의 끝에 있는 사용자 지정 규칙 유형도 편집 해야 합니다.

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

13. 온라인 편집기에서 **저장** 을 클릭 합니다.

14. **기본**  >  **구독** 을 클릭 하 여 대상 nsg를 배포할 구독을 선택 합니다.

15. **기본**  >  **리소스 그룹** 을 클릭 하 여 대상 nsg가 배포 될 리소스 그룹을 선택 합니다.  **새로 만들기** 를 클릭 하 여 대상 nsg에 대 한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 NSG의 원본 리소스 그룹과 같지 않은지 확인 합니다.

16. **기본**  >  **위치가** nsg를 배포 하려는 대상 위치로 설정 되어 있는지 확인 합니다.

17. 위의 매개 변수 편집기에서 입력 한 이름과 이름이 일치 하는 **설정** 아래를 확인 합니다.

18. 사용 **약관**아래 상자를 선택 합니다.

19. **구매** 단추를 클릭 하 여 대상 네트워크 보안 그룹을 배포 합니다.

## <a name="discard"></a>취소

대상 NSG를 삭제 하려는 경우 대상 NSG를 포함 하는 리소스 그룹을 삭제 합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택 하 고 개요 페이지의 위쪽에서 **삭제** 를 선택 합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료 하려면 원본 NSG 또는 리소스 그룹을 삭제 합니다. 이렇게 하려면 포털의 대시보드에서 네트워크 보안 그룹 또는 리소스 그룹을 선택 하 고 각 페이지의 위쪽에서 **삭제** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
