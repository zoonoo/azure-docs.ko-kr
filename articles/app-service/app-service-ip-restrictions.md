---
title: Azure App Service 액세스 제한
description: 액세스 제한을 설정 하 여 Azure App Service에서 앱을 보호 하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576447"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Azure App Service 액세스 제한 설정

액세스 제한을 설정 하 여 앱에 대 한 네트워크 액세스를 제어 하는 우선 순위가 지정 된 허용/거부 목록을 정의할 수 있습니다. 이 목록에는 IP 주소 또는 Azure Virtual Network 서브넷이 포함 될 수 있습니다. 하나 이상의 항목이 있는 경우 암시적 *거부는 모두* 목록의 끝에 있습니다.

액세스 제한 기능은 모든 Azure App Service 호스팅 워크 로드와 함께 작동 합니다. 워크 로드에는 웹 앱, API 앱, Linux 앱, Linux 컨테이너 앱 및 함수가 포함 될 수 있습니다.

앱에 대 한 요청이 수행 되 면 액세스 제한 목록의 IP 주소 규칙에 대해 보낸 사람 주소가 평가 됩니다. 보낸 사람 주소가 Microsoft 웹에 대 한 서비스 끝점으로 구성 된 서브넷에 있는 경우 원본 서브넷은 액세스 제한 목록의 가상 네트워크 규칙과 비교 됩니다. 목록의 규칙에 따라 주소가 액세스를 허용 하지 않는 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드를 사용 하 여 응답 합니다.

액세스 제한 기능은 코드가 실행 되는 작업자 호스트의 업스트림 인 App Service 프런트 엔드 역할에서 구현 됩니다. 따라서 액세스 제한은 사실상 네트워크 Acl (액세스 제어 목록)입니다.

Azure 가상 네트워크에서 웹 앱에 대 한 액세스를 제한 하는 기능은 [서비스 끝점][serviceendpoints]에 의해 활성화 됩니다. 서비스 끝점을 사용 하 여 선택한 서브넷에서 다중 테 넌 트 서비스에 대 한 액세스를 제한할 수 있습니다. App Service Environment에서 호스트 되는 앱에 대 한 트래픽을 제한 하는 것은 작동 하지 않습니다. App Service Environment 하는 경우 IP 주소 규칙을 적용 하 여 앱에 대 한 액세스를 제어할 수 있습니다.

> [!NOTE]
> 서비스 끝점은 사용 하도록 설정 되는 Azure 서비스와 네트워킹 측 모두에서 사용 하도록 설정 되어야 합니다. 서비스 끝점을 지 원하는 Azure 서비스 목록은 [Virtual Network 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 참조 하세요.
>

![액세스 제한 흐름 다이어그램](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>포털에서 액세스 제한 규칙 추가 또는 편집

앱에 액세스 제한 규칙을 추가 하려면 다음을 수행 합니다.

1. Azure Portal에 로그인합니다.

1. 왼쪽 창에서 **네트워킹** 을 선택 합니다.

1. **네트워킹** 창의 **액세스 제한** 에서 **액세스 제한 구성** 을 선택 합니다.

   ![Azure Portal App Service 네트워킹 옵션 창의 스크린샷](media/app-service-ip-restrictions/access-restrictions.png)  

1. **액세스 제한** 페이지에서 앱에 대해 정의 된 액세스 제한 규칙의 목록을 검토 합니다.

   ![선택한 앱에 대해 정의 된 액세스 제한 규칙 목록을 보여 주는 Azure Portal의 액세스 제한 페이지 스크린샷](media/app-service-ip-restrictions/access-restrictions-browse.png)

   목록에는 앱에 적용 되는 현재 제한이 모두 표시 됩니다. 앱에 대 한 가상 네트워크 제한이 있는 경우이 표에는 서비스 끝점이 Microsoft 웹에 대해 사용 하도록 설정 되어 있는지 여부가 표시 됩니다. 앱에 제한이 정의 되어 있지 않으면 어디에서 나 앱에 액세스할 수 있습니다.  

### <a name="add-an-access-restriction-rule"></a>액세스 제한 규칙 추가

앱에 액세스 제한 규칙을 추가 하려면 **액세스 제한** 창에서 **규칙 추가** 를 선택 합니다. 규칙을 추가 하면 즉시 적용 됩니다. 

규칙은 우선 **순위** 열에서 가장 낮은 숫자부터 시작 하 여 우선 순위 순서 대로 적용 됩니다. 암시적 *거부* 는 단일 규칙을 추가한 후에도 적용 됩니다.

**IP 제한 추가** 창에서 규칙을 만들 때 다음을 수행 합니다.

1. **작업** 아래에서 **허용** 또는 **거부** 를 선택 합니다.  

   !["IP 제한 추가" 창의 스크린샷](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. 필요에 따라 규칙의 이름과 설명을 입력 합니다.  
1. **유형** 드롭다운 목록에서 규칙의 유형을 선택 합니다.  
1. **우선 순위** 상자에 우선 순위 값을 입력 합니다.  
1. **구독** , **Virtual Network** 및 **서브넷** 드롭다운 목록에서 액세스를 제한 하려는 항목을 선택 합니다.  

### <a name="set-an-ip-address-based-rule"></a>IP 주소 기반 규칙 설정

이전 섹션에 설명 된 절차를 따르고 다음과 같은 변형이 있습니다.
* 3 단계의 경우 **유형** 드롭다운 목록에서 **IPv4** 또는 **IPv6** 을 선택 합니다. 

IPv4 및 IPv6 주소에 대 한 CIDR (CIDR) 표기법 Inter-Domain으로 IP 주소를 지정 합니다. 주소를 지정 하려면 *1.2.3.4/32* 와 같은 항목을 사용할 수 있습니다. 여기서 처음 4 개의 8 진수는 IP 주소를 나타내고 */32* 는 마스크입니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대해 자세히 알아보려면 클래스 없는 [Inter-Domain 라우팅](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조 하세요. 

## <a name="use-service-endpoints"></a>서비스 엔드포인트 사용

서비스 끝점을 사용 하 여 선택한 Azure virtual network 서브넷에 대 한 액세스를 제한할 수 있습니다. 특정 서브넷에 대 한 액세스를 제한 하려면 **Virtual Network** 유형을 사용 하 여 제한 규칙을 만듭니다. 그런 다음 액세스를 허용 하거나 거부할 구독, 가상 네트워크 및 서브넷을 선택할 수 있습니다. 

선택한 서브넷에 대해 서비스 끝점이 아직 Microsoft 웹에서 사용 하도록 설정 되지 않은 경우 **누락 된 microsoft. 웹 서비스 끝점 무시** 확인란을 선택 하지 않으면 자동으로 사용 하도록 설정 됩니다. 앱에서 서비스 끝점을 사용 하도록 설정 하 고 서브넷은 사용 하지 않을 수 있는 시나리오는 주로 서브넷에서 사용 하도록 설정할 수 있는 권한이 있는지 여부에 따라 달라 집니다. 

다른 사용자가 서브넷에서 서비스 끝점을 사용 하도록 설정 해야 하는 경우 **누락 된 Microsoft 웹 서비스 끝점 무시** 확인란을 선택 합니다. 앱은 나중에 서브넷에서 사용 하도록 설정 하는 것을 예측 하 여 서비스 끝점에 대해 구성 됩니다. 

![Virtual Network 유형이 선택 된 "IP 제한 추가" 창의 스크린샷](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

서비스 끝점을 사용 하 여 App Service Environment에서 실행 되는 앱에 대 한 액세스를 제한할 수 없습니다. 앱이 App Service Environment에 있는 경우 IP 액세스 규칙을 적용 하 여 해당 앱에 대 한 액세스를 제어할 수 있습니다. 

서비스 끝점을 사용 하면 응용 프로그램 게이트웨이 또는 WAF (웹 응용 프로그램 방화벽) 장치를 사용 하 여 앱을 구성할 수 있습니다. 또한 보안 백 엔드를 사용 하 여 다중 계층 응용 프로그램을 구성할 수 있습니다. 자세한 내용은 [네트워킹 기능 및 App Service](networking-features.md) 와 [서비스 끝점과의 통합 Application Gateway](networking/app-gateway-with-service-endpoints.md)을 참조 하세요.

> [!NOTE]
> - 서비스 끝점은 현재 IP SSL(Secure Sockets Layer) (SSL) VIP (가상 IP)를 사용 하는 웹 앱에 대해 지원 되지 않습니다.
> - IP 또는 서비스 끝점 제한의 행은 512 개로 제한 됩니다. 제한 행이 512 개를 초과 해야 하는 경우 Azure Front 도어, Azure 앱 게이트웨이 또는 WAF와 같은 독립 실행형 보안 제품을 설치 하는 것이 좋습니다.
>

## <a name="manage-access-restriction-rules"></a>액세스 제한 규칙 관리

기존 액세스 제한 규칙을 편집 하거나 삭제할 수 있습니다.

### <a name="edit-a-rule"></a>규칙 편집

1. 기존 액세스 제한 규칙 편집을 시작 하려면 **액세스 제한** 페이지에서 편집 하려는 규칙을 두 번 클릭 합니다.

1. **IP 제한 편집** 창에서 변경한 후 **규칙 업데이트** 를 선택 합니다. 편집은 우선 순위 순서 변경을 포함 하 여 즉시 적용 됩니다.

   ![기존 액세스 제한 규칙의 필드를 표시 하는 Azure Portal의 "IP 제한 편집" 창의 스크린샷](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > 규칙을 편집 하는 경우 IP 주소 규칙과 가상 네트워크 규칙 간을 전환할 수 없습니다. 

   ![가상 네트워크 규칙에 대 한 설정을 보여 주는 Azure Portal의 "IP 제한 편집" 창의 스크린샷](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>규칙 삭제

규칙을 삭제 하려면 **액세스 제한** 페이지에서 삭제 하려는 규칙 옆에 있는 줄임표 **(...** )를 선택한 다음 **제거** 를 선택 합니다.

![삭제할 액세스 제한 규칙 옆의 "제거" 줄임표를 표시 하는 "액세스 제한" 페이지의 스크린샷](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>단일 IP 주소 차단

첫 번째 IP 제한 규칙을 추가 하는 경우 서비스는 우선 순위가 2147483647 인 명시적 *거부 모든* 규칙을 추가 합니다. 실제로 명시적 *거부* 규칙은 실행 되는 최종 규칙이 며 *허용* 규칙에 의해 명시적으로 허용 되지 않는 모든 IP 주소에 대 한 액세스를 차단 합니다.

단일 IP 주소 또는 IP 주소 블록을 명시적으로 차단 하지만 다른 모든 항목에 대 한 액세스를 허용 하려는 시나리오의 경우 명시적 *허용 모든* 규칙을 추가 합니다.

![차단 된 단일 IP 주소를 보여 주는 Azure Portal의 "액세스 제한" 페이지 스크린샷](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>SCM 사이트에 대 한 액세스 제한 

앱에 대 한 액세스를 제어 하는 것 외에도 앱에서 사용 하는 SCM 사이트에 대 한 액세스를 제한할 수 있습니다. SCM 사이트는 웹 배포 끝점과 Kudu 콘솔 둘 다에 해당 합니다. 앱에서 SCM 사이트에 대 한 액세스 제한을 별도로 할당 하거나 앱과 SCM 사이트에 대해 동일한 제한 집합을 사용할 수 있습니다. 확인란을 선택 하는 것과 **같은 제한 사항을 \<app name>** 선택 하는 경우 모든 항목은 숨겨집니다. 확인란의 선택을 취소 하면 SCM 사이트 설정이 다시 적용 됩니다. 

![SCM 사이트 또는 앱에 대 한 액세스 제한이 설정 되지 않음을 보여 주는 Azure Portal의 "액세스 제한" 페이지 스크린샷](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>프로그래밍 방식으로 액세스 제한 규칙 관리

다음 중 하나를 수행 하 여 프로그래밍 방식으로 액세스 제한을 추가할 수 있습니다. 

* [Azure CLI를](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true)사용 합니다. 예를 들면 다음과 같습니다.
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true)를 사용 합니다. 예를 들면 다음과 같습니다.


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

다음 중 하나를 수행 하 여 수동으로 값을 설정할 수도 있습니다.

* Azure Resource Manager에서 앱 구성에 대 한 [Azure REST API](/rest/api/azure/) PUT 작업을 사용 합니다. Azure Resource Manager에서이 정보에 대 한 위치는 다음과 같습니다.

  management.azure.com/subscriptions/ **구독 ID** /resourceGroups/ **리소스 그룹** /providers/Microsoft.Web/sites/ **웹앱 이름** /config/web?api-version=2018-02-01

* ARM 템플릿을 사용 합니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

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

## <a name="set-up-azure-functions-access-restrictions"></a>Azure Functions 액세스 제한 설정

App Service 계획과 동일한 기능을 사용 하는 함수 앱에 대 한 액세스 제한도 사용할 수 있습니다. 액세스 제한을 사용 하는 경우 허용 되지 않는 Ip에 대해 Azure Portal 코드 편집기도 사용 하지 않도록 설정 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Functions에 대 한 액세스 제한](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[서비스 끝점과 Application Gateway 통합](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
