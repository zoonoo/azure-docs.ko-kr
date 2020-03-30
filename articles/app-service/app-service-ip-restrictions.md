---
title: Azure 앱 서비스 액세스 제한
description: 액세스 제한을 지정하여 Azure 앱 서비스에서 앱을 보호하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a02c099871ce9748f4c5f604900a7c4d57bb96b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473526"
---
# <a name="azure-app-service-access-restrictions"></a>Azure 앱 서비스 액세스 제한 #

액세스 제한을 사용하면 앱에 대한 네트워크 액세스를 제어하는 우선 순위의 허용/거부 목록을 정의할 수 있습니다. 목록에는 IP 주소 또는 Azure 가상 네트워크 서브넷이 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 있는 암시적 "모두 거부"가 있습니다.

액세스 제한 기능은 다음을 포함하여 모든 App Service 호스팅 작업 로드와 함께 작동합니다. 웹 앱, API 앱, Linux 앱, Linux 컨테이너 앱 및 기능.

앱에 대한 요청이 이루어지면 액세스 제한 목록의 IP 주소 규칙에 대해 FROM 주소가 평가됩니다. FROM 주소가 Microsoft.Web에 대한 서비스 끝점으로 구성된 서브넷에 있는 경우 원본 서브넷은 액세스 제한 목록의 가상 네트워크 규칙과 비교됩니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

액세스 제한 기능은 코드가 실행되는 작업자 호스트의 업스트림인 App Service 프런트 엔드 역할에 구현됩니다. 따라서 액세스 제한은 효과적으로 네트워크 ACL입니다.

Azure 가상 네트워크(VNet)에서 웹 앱에 대한 액세스를 제한하는 기능을 [서비스 끝점이라고][serviceendpoints]합니다. 서비스 끝점을 사용하면 선택한 서브넷에서 다중 테넌트 서비스에 대한 액세스를 제한할 수 있습니다. 네트워킹 측면과 활성화 중인 서비스에서 모두 활성화해야 합니다. 앱 서비스 환경에서 호스팅되는 앱으로 트래픽을 제한하는 것은 작동하지 않습니다. 앱 서비스 환경에 있는 경우 IP 주소 규칙을 사용하여 앱에 대한 액세스를 제어할 수 있습니다.

![액세스 제한 흐름](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>포털에서 액세스 제한 규칙 추가 및 편집 ##

앱에 액세스 제한 규칙을 추가하려면 메뉴를 사용하여 **네트워크**>**액세스 제한을** 열고 액세스 제한 **구성을** 클릭합니다.

![App Service 네트워킹 옵션](media/app-service-ip-restrictions/access-restrictions.png)  

액세스 제한 UI에서 앱에 대해 정의된 액세스 제한 규칙 목록을 검토할 수 있습니다.

![목록 액세스 제한](media/app-service-ip-restrictions/access-restrictions-browse.png)

목록에는 앱에 있는 모든 현재 제한 사항이 표시됩니다. 앱에 VNet 제한이 있는 경우 Microsoft.Web에 대해 서비스 끝점을 사용하도록 설정했는지 표에 표시됩니다. 앱에 정의된 제한이 없는 경우 어디서나 앱에 액세스할 수 있습니다.  

## <a name="adding-ip-address-rules"></a>IP 주소 규칙 추가

**[+] 규칙 추가를** 클릭하여 새 액세스 제한 규칙을 추가할 수 있습니다. 규칙을 추가하면 즉시 적용됩니다. 규칙은 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 모두 거부가 적용됩니다.

규칙을 만들 때 허용/거부 및 규칙 유형을 선택해야 합니다. 또한 우선 순위 값과 액세스를 제한하는 것을 제공해야 합니다.  선택적으로 규칙에 이름과 설명을 추가할 수 있습니다.  

![IP 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP 주소 기반 규칙을 설정하려면 IPv4 또는 IPv6 유형을 선택합니다. IP 주소 표기법은 IPv4 및 IPv6 주소 둘 다에 대해 CIDR 표기법으로 지정해야 합니다. 정확한 주소를 지정하려면 1.2.3.4/32와 같이 처음 4개의 8진수로 IP 주소를 나타내고 /32로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요. 

## <a name="service-endpoints"></a>서비스 엔드포인트

서비스 끝점을 사용하면 선택한 Azure 가상 네트워크 서브넷에 대한 액세스를 제한할 수 있습니다. 특정 서브넷에 대한 액세스를 제한하려면 가상 네트워크 유형으로 제한 규칙을 만듭니다. 액세스를 허용하거나 거부할 구독, VNet 및 서브넷을 선택할 수 있습니다. 선택한 서브넷에 대해 Microsoft.Web에서 서비스 끝점을 아직 활성화하지 않은 경우 해당 작업을 수행하지 말라는 확인란을 선택하지 않으면 자동으로 활성화됩니다. 앱에서 사용하도록 설정하려고 하지만 서브넷이 아닌 경우 서브넷에서 서비스 끝점을 활성화할 수 있는 권한이 있는 경우와 관련이 있습니다. 서브넷에서 서비스 끝점을 사용하도록 설정하려면 다른 사람을 요청해야 하는 경우 서브넷에서 나중에 활성화될 것으로 예상하여 확인란을 선택하고 서비스 끝점에 대해 앱을 구성하도록 할 수 있습니다. 

![VNet 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

서비스 끝점은 앱 서비스 환경에서 실행되는 앱에 대한 액세스를 제한하는 데 사용할 수 없습니다. 앱이 앱 서비스 환경에 있는 경우 IP 액세스 규칙을 사용하여 앱에 대한 액세스를 제어할 수 있습니다. 

서비스 끝점을 사용하면 응용 프로그램 게이트웨이 또는 기타 WAF 장치로 앱을 구성할 수 있습니다. 또한 보안 백엔드를 통해 다중 계층 응용 프로그램을 구성할 수도 있습니다. 몇 가지 가능성에 대한 자세한 내용은 [네트워킹 기능 및 앱 서비스](networking-features.md) 및 서비스 [끝점과의 응용 프로그램 게이트웨이 통합을](networking/app-gateway-with-service-endpoints.md)참조하십시오.

## <a name="managing-access-restriction-rules"></a>액세스 제한 규칙 관리

모든 행을 클릭하여 기존 액세스 제한 규칙을 편집할 수 있습니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

규칙을 편집할 때 IP 주소 규칙과 가상 네트워크 규칙 사이의 형식을 변경할 수 없습니다. 

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

규칙을 삭제하려면 규칙에서 **...를** 클릭한 다음 **제거를**클릭합니다.

![액세스 제한 규칙 삭제](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>단일 IP 주소 차단 ##

첫 번째 IP 제한 규칙을 추가하면 서비스는 2147483647의 우선 순위를 가진 **명시적 거부 모든** 규칙을 추가합니다. 실제로 명시적 **거부 모든** 규칙은 마지막 규칙이 실행되고 **허용** 규칙을 사용하여 명시적으로 허용되지 않는 IP 주소에 대한 액세스를 차단합니다.

사용자가 단일 IP 주소 또는 IP 주소 블록을 명시적으로 차단하고 다른 모든 액세스를 허용하려는 시나리오의 경우 명시적 **모두 허용** 규칙을 추가해야 합니다.

![단일 IP 주소 블록](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM 사이트 

앱에 대한 액세스를 제어할 수 있을 뿐만 아니라 앱에서 사용하는 scm 사이트에 대한 액세스를 제한할 수도 있습니다. scm 사이트는 웹 배포 끝점이자 Kudu 콘솔입니다. 앱에서 scm 사이트에 대한 액세스 제한을 별도로 할당하거나 앱과 scm 사이트 모두에 대해 동일한 집합을 사용할 수 있습니다. 앱과 동일한 제한을 갖도록 확인란을 선택하면 모든 것이 비워지게 됩니다. 확인란을 선택 취소하면 scm 사이트에서 이전에 설정했던 설정이 적용됩니다. 

![목록 액세스 제한](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>액세스 제한 규칙의 프로그래밍 방식 조작 ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) 및 [Azure PowerShell은](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) 액세스 제한 편집을 지원합니다. Azure CLI를 사용하여 액세스 제한을 추가하는 예:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Azure PowerShell을 사용하여 액세스 제한을 추가하는 예:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

리소스 관리자의 앱 구성에서 [Azure REST](https://docs.microsoft.com/rest/api/azure/) PUT 작업을 사용하거나 Azure 리소스 관리자 템플릿을 사용하여 값을 수동으로 설정할 수도 있습니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

Resource Manager에서 이 정보는 다음 위치에 제공됩니다.

management.azure.com/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹**/providers/Microsoft.Web/sites/**웹앱 이름**/config/web?api-version=2018-02-01

앞의 예제에 대한 JSON 구문은 다음과 같습니다.
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-function-app-access-restrictions"></a>Azure 함수 앱 액세스 제한

앱 서비스 요금제와 동일한 기능을 가진 기능 앱에도 액세스 제한을 사용할 수 있습니다. 액세스 제한을 사용하도록 설정하면 허용되지 않는 IP에 대해 포털 코드 편집기가 비활성화됩니다.

## <a name="next-steps"></a>다음 단계
[Azure 기능 앱에 대한 액세스 제한](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[서비스 엔드포인트와의 애플리케이션 게이트웨이 통합](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
