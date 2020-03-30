---
title: Azure 포털을 사용하여 Azure 공용 IP를 다른 Azure 지역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 사용하여 Azure 공용 IP를 한 Azure 리전에서 다른 Azure 지역으로 이동합니다.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641404"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 공용 IP를 다른 지역으로 이동

기존 Azure 공용 IP를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성과 sku를 사용하여 공용 IP를 만들 수 있습니다. 재해 복구 계획의 일환으로 공용 IP를 다른 지역으로 이동할 수도 있습니다.

Azure 공용 IP는 지역에 따라 다르며 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 공용 IP의 기존 구성을 내보낼 수 있습니다.  그런 다음 공용 IP를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 지역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다.  리소스 관리자 및 템플릿에 대한 자세한 내용은 [빠른 시작: Azure 포털을 사용하여 Azure 리소스 관리자 템플릿 만들기 및 배포를](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- Azure Public IP가 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 공용 IP는 리전 간에 이동할 수 없습니다.  새 공용 IP를 대상 지역의 리소스에 연결해야 합니다.

- 공용 IP 구성을 내보내고 템플릿을 배포하여 다른 리전에서 공용 IP를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸러버, NSG(네트워크 보안 그룹) 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 사용되는 대상 지역에서 공용 IP를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 공용 IP 추가를 지원할 수 있는 리소스가 충분한지 확인합니다.  A[zure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용하여 구성 이동에 대한 공용 IP를 준비하고 공용 IP 구성을 Azure 포털을 사용하여 대상 영역으로 이동하는 방법을 보여 주십니다.

### <a name="export-the-template-and-deploy-from-a-script"></a>템플릿 내보내기 및 스크립트에서 배포

1. [Azure 포털](https://portal.azure.com) > **리소스 그룹에**로그인합니다.
2. 원본 공용 IP가 포함된 리소스 그룹을 찾아 클릭합니다.
3. > **설정** > 내보내기 템플릿 을**선택합니다.**
4. **내보내기 템플릿** 블레이드에서 **배포를** 선택합니다.
5. **템플릿** > **편집 매개변수를** 클릭하여 온라인 편집기에서 **parameters.json** 파일을 엽니다.
8. 공용 IP 이름의 매개 변수를 편집하려면 소스 공용 IP 이름에서 매개 **변수** > **값아래의** 속성을 대상 공용 IP 이름으로 변경하고 이름이 따옴표에 있는지 확인합니다.

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
8.  편집기에서 **저장을** 클릭합니다.

9.  **템플릿** > **편집 템플릿을** 클릭하여 온라인 편집기에서 **template.json** 파일을 엽니다.

10. 공용 IP가 이동할 대상 영역을 편집하려면 **리소스**에서 **위치** 속성을 변경합니다.

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

11. 지역 위치 코드를 얻으려면 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하십시오.  지역의 코드는 공백이 없는 지역 이름, **미국** = **중부 중앙입니다.**

12. 원하는 경우 템플릿의 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

    * **Sku** - **template.json** 파일에서 **sku** > **이름** 속성을 변경하여 구성에서 공용 IP의 sku를 표준에서 기본 또는 기본으로 변경할 수 있습니다.

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

        기본 및 표준 sku 공용 IP의 차이점에 대한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)참조하십시오.

    * **공용 IP 할당 방법** 및 **유휴 시간 초과** - **publicIPAllocationMethod** 속성을 **동적에서** **정적** 또는 **정적에서** **동적으로**변경하여 템플릿에서 이러한 옵션을 모두 변경할 수 있습니다. 유휴 시간 초과는 **idleTimeoutInMinutes** 속성을 원하는 양으로 변경하여 변경할 수 있습니다.  기본값은 **4:**

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


13. 온라인 편집기에서 **저장을** 클릭합니다.

14. **BASICS** > **구독을** 클릭하여 대상 공용 IP가 배포될 구독을 선택합니다.

15. **BASICS** > **리소스 그룹을** 클릭하여 대상 공용 IP를 배포할 리소스 그룹을 선택합니다.  **새 만들기를** 클릭하여 대상 공용 IP에 대한 새 리소스 그룹을 만들 수 있습니다.  이름이 기존 원본 공용 IP의 소스 리소스 그룹과 같지 않은지 확인합니다.

16. **BASICS** > **위치** 확인은 공용 IP를 배포할 대상 위치로 설정됩니다.

17. **설정에서** 이름이 위의 매개 변수 편집기에 입력한 이름과 일치하는지 확인합니다.

18. **이용 약관에서 확인란을**선택합니다.

19. **구매** 버튼을 클릭하여 대상 공용 IP를 배포합니다.

## <a name="discard"></a>취소

대상 공용 IP를 삭제하려면 대상 공용 IP가 포함된 리소스 그룹을 삭제합니다.  이렇게 하려면 포털의 대시보드에서 리소스 그룹을 선택하고 개요 페이지 상단에서 **삭제를** 선택합니다.

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 공용 IP의 이동을 완료하려면 원본 공용 IP 또는 리소스 그룹을 삭제합니다. 이렇게 하려면 포털의 대시보드에서 공용 IP 또는 리소스 그룹을 선택하고 각 페이지 상단에서 **삭제를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Public IP를 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
