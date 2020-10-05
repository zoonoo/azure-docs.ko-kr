---
title: '자습서: Azure Front Door와 Azure WAF(Web Application Firewall)를 사용하여 웹앱 확장 및 보호'
description: 이 자습서는 Azure Front Door Service에서 Azure Web Application Firewall을 사용하는 방법을 보여줍니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 7c5e938f985296e0534ca6e2438cf3acedb0fb65
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626482"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>자습서: Azure Front Door와 Azure WAF(Web Application Firewall)를 사용하여 웹 애플리케이션을 신속하게 확장 및 보호

최근 몇 주 동안 COVID-19로 인해 많은 웹 애플리케이션의 트래픽이 급증했습니다. 이러한 웹 애플리케이션에는 서비스 거부 공격을 비롯한 악성 트래픽이 급증하는 현상도 나타나고 있습니다. 트래픽 급증에 맞게 애플리케이션을 확장하고, 공격으로부터 보호하는 효과적인 방법은 Azure WAF를 사용하는 Azure Front Door를 웹앱 앞에 가속, 캐싱 및 보안 계층으로 설정하는 것입니다. 이 문서는 Azure 내부 또는 외부에서 실행되는 모든 웹앱에 대해 Azure WAF를 구성하여 Azure Front Door를 확보하는 방법에 대한 지침을 제공합니다. 

이 자습서에서는 Azure CLI를 사용하여 WAF를 구성합니다. Azure Portal, Azure PowerShell, Azure Resource Manager 또는 Azure REST API를 사용하여 동일한 작업을 수행할 수 있습니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> - Front Door를 만듭니다.
> - Azure WAF 정책을 만듭니다.
> - WAF 정책에 대한 규칙 집합을 구성합니다.
> - WAF 정책을 Front Door와 연결합니다.
> - 사용자 지정 도메인을 구성합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 이 자습서의 지침은 Azure CLI를 사용합니다. [이 가이드를 참조](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true)하여 Azure CLI를 시작하세요.

  > [!TIP] 
  > Azure CLI를 시작하는 쉽고 빠른 방법 [Azure Cloud Shell의 Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)를 사용하는 것입니다.

- `front-door` 확장이 Azure CLI에 추가되어 있는지 확인합니다.

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> 이 자습서에서 사용되는 명령에 대한 자세한 내용은 [Front Door에 대한 Azure CLI 참조](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest&preserve-view=true)를 참조하세요.

## <a name="create-an-azure-front-door-resource"></a>Azure Front Door 리소스 만들기

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: 보호하려는 애플리케이션의 FQDN(정규화된 도메인 이름) 이름입니다. 예들 들어 `myapplication.contoso.com`입니다.

`--accepted-protocols`: Azure Front Door가 웹 애플리케이션에 대해 지원할 프로토콜을 지정합니다. 예들 들어 `--accepted-protocols Http Https`입니다.

`--name`: Azure Front Door 리소스의 이름입니다.

`--resource-group`: 이 Azure Front Door 리소스를 배치할 리소스 그룹입니다. 리소스 그룹에 대해 자세히 알아보려면 [Azure에서 리소스 그룹 관리](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)를 참조하세요.

이 명령을 실행할 때 받는 응답에서 `hostName` 키를 찾습니다. 이후 단계에서 이 값이 필요합니다. `hostName`은 직접 만든 Azure Front Door 리소스의 DNS 이름입니다.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure Front Door 리소스에 사용할 Azure WAF 프로필 만들기

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: 새 Azure WAF 정책의 이름입니다.

`--resource-group`: 이 WAF 리소스를 배치할 리소스 그룹입니다. 

앞의 CLI 코드는 사용하도록 설정되고 방지 모드에 있는 WAF 정책을 만듭니다. 

> [!NOTE] 
> 보호 모드를 사용하기로 결정하기 전에, 탐지 모드에서 WAF 정책을 생성하여 악성 요청을(차단하지 않고) 어떻게 탐지하고 기록하는지 관찰할 수 있습니다.

이 명령을 실행할 때 받는 응답에서 `ID` 키를 찾습니다. 이후 단계에서 이 값이 필요합니다. 

`ID` 필드는 다음과 같은 형식이어야 합니다.

/subscriptions/**구독 ID**/resourcegroups/**리소스 그룹 이름**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF 정책 이름**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>관리형 규칙 집합을 WAF 정책에 추가

관리형 규칙 집합을 WAF 정책에 추가할 수 있습니다. 관리형 규칙 집합은 Microsoft에서 구축하고 관리하는 규칙 집합으로, 여러 위협으로부터 사용자를 보호하는 데 도움이 됩니다. 이 예제에서는 두 가지 규칙 집합을 추가합니다.
- 기본 규칙 집합 - 일반적인 웹 위협으로부터 사용자를 보호하는 데 유용합니다. 
- 봇 보호 규칙 집합 - 악성 봇으로부터 사용자를 보호하는 데 유용합니다.

기본 규칙 집합 추가:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

봇 보호 규칙 집합 추가:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Azure WAF 리소스에 대해 지정한 이름입니다

`--resource-group`: WAF 리소스를 배치한 리소스 그룹입니다.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>WAF 정책을 Azure Front Door 리소스와 연결

이 단계에서는 직접 만든 WAF 정책을 웹 애플리케이션 앞에 있는 Azure Front Door 리소스와 연결합니다.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Azure Front Door 리소스에 대해 지정한 이름입니다

`--resource-group`: Azure Front Door 리소스를 배치한 리소스 그룹입니다.

`--set`: Azure Front Door 리소스와 연결된 `frontendEndpoint`의 `WebApplicationFirewallPolicyLink` 특성을 새 WAF 정책으로 업데이트합니다. 이 자습서의 앞부분에서 WAF 프로필을 만들 때 받은 응답에 있는 WAF 정책의 ID가 있어야 합니다.

 > [!NOTE] 
> 위의 예는 사용자 지정 도메인을 사용하지 않는 경우 적용됩니다. 사용자 지정 도메인을 사용하여 웹 애플리케이션에 액세스하는 경우가 아니면 다음 섹션을 건너뛸 수 있습니다. 이 경우 Azure Front Door 리소스를 만들 때 얻은 `hostName`을 고객에게 제공합니다. 이 `hostName`을 사용하여 웹 애플리케이션으로 이동합니다.

## <a name="configure-the-custom-domain-for-your-web-application"></a>웹 애플리케이션에 대한 사용자 지정 도메인 구성

웹 애플리케이션의 사용자 지정 도메인 이름은 고객이 애플리케이션을 참조하는 데 사용하는 이름입니다. 예: www.contoso.com. 처음에는 사용자 지정 도메인 이름이 Azure Front Door를 도입하기 전에 실행 중이던 위치를 가리킵니다. Azure Front Door 및 WAF를 애플리케이션에 추가한 후에는 사용자 지정 도메인에 해당하는 DNS 항목이 Azure Front Door 리소스를 가리켜야 합니다. 이러한 변경은 DNS 서버의 항목을 Azure Front Door 리소스를 만들 때 적어둔 Azure Front Door `hostName`에 다시 매핑하여 수행할 수 있습니다.

DNS 레코드를 업데이트하는 구체적인 단계는 DNS 서비스 공급자에 따라 다릅니다. Azure DNS를 사용하여 DNS 이름을 호스트하는 경우 [DNS 레코드를 업데이트하는 단계](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli)에 대한 설명서를 참조하고 Azure Front Door `hostName`을 가리킵니다. 

고객이 영역 루트(예: contoso.com)를 사용하여 웹 사이트에 액세스해야 하는 경우 유의해야 하는 중요한 사항이 있습니다. 이 경우 Azure DNS 및 해당 [별칭 레코드 유형](https://docs.microsoft.com/azure/dns/dns-alias)을 사용하여 DNS 이름을 호스트해야 합니다. 

또한 매핑을 인식할 수 있도록 Azure Front Door 구성을 업데이트하여 [사용자 지정 도메인을 추가](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)해야 합니다.

마지막으로, 사용자 지정 도메인을 사용하여 웹 애플리케이션에 도달하고 HTTPS 프로토콜을 사용하려는 경우입니다. [Azure Front Door에서 사용자 지정 도메인에 대한 인증서를 설정](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)해야 합니다. 

## <a name="lock-down-your-web-application"></a>웹 애플리케이션 잠금

Azure Front Door 에지만 웹 애플리케이션과 통신할 수 있도록 하는 것이 좋습니다. 이렇게 하면 아무도 Azure Front Door 보호를 우회하고 애플리케이션에 직접 액세스할 수 없습니다. 이러한 잠금을 설정하려면 [내 백엔드에 Azure Front Door만 액세스하도록 잠그는 방법은 무엇인가요?](https://docs.microsoft.com/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에 사용된 리소스가 더 이상 필요 없으면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) 명령을 사용하여 리소스 그룹, Front Door, WAF 정책을 제거합니다.

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: 이 자습서에서 사용된 모든 리소스에 대한 리소스 그룹의 이름입니다.

## <a name="next-steps"></a>다음 단계

Front Door의 문제를 해결하는 방법을 알아보려면 문제 해결 가이드를 참조하세요.

> [!div class="nextstepaction"]
> [일반적인 라우팅 문제 해결](front-door-troubleshoot-routing.md)
