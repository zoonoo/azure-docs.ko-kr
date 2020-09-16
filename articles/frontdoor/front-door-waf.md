---
title: Azure Front Door와 Azure WAF(웹 애플리케이션 방화벽)를 사용하여 웹 애플리케이션을 신속하게 확장 및 보호 | Microsoft Docs
description: 이 자습서는 Azure Front Door Service에서 웹 애플리케이션 방화벽을 사용하는 방법을 안내합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1958481193b66c8cec2cb6a1ac6648a6900d70ac
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531205"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>자습서: Azure Front Door와 Azure WAF(웹 애플리케이션 방화벽)를 사용하여 웹 애플리케이션을 신속하게 확장 및 보호

COVID-19와 관련하여 최근 몇 주 동안 많은 웹 애플리케이션의 트래픽이 급증했습니다. 게다가, 이러한 웹 애플리케이션에는 서비스 거부 공격을 포함한 악의적인 트래픽도 급증하는 것으로 보입니다. 이러한 요구를 모두 처리하고, 트래픽 급증에 맞게 확장하고, 공격으로부터 보호하는 효과적인 방법은 웹 애플리케이션 앞에 가속, 캐싱 및 보안 계층으로 Azure WAF를 사용하여 Azure Front Door를 설정하는 것입니다. 이 문서는 Azure 내외에서 실행되는 모든 웹 애플리케이션에 대해 Azure WAF 설정을 통해 Azure Front Door를 빠르게 확보하는 방법에 대한 지침을 제공합니다. 

이 자습서에서는 Azure CLI를 사용하여 WAF를 설정하지만 Azure Portal, Azure PowerShell, Azure ARM 및 Azure REST API에서도 모든 단계가 완벽하게 지원됩니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
> - Front Door를 만듭니다.
> - Azure WAF 정책을 만듭니다.
> - WAF 정책에 대한 규칙 집합을 구성합니다.
> - WAF 정책을 Front Door와 연결
> - 사용자 지정 도메인 구성

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 블로그의 지침에서는 Azure CLI(명령줄 인터페이스)를 사용합니다. 이 가이드를 참조하여 [Azure CLI를 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)하세요.

팁: Azure CLI에서 시작하는 쉽고 빠른 방법은 [Azure Cloud Shell의 Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)를 사용하는 것입니다.

front-door 확장이 Azure CLI에 추가되어 있는지 확인

```azurecli-interactive 
az extension add --name front-door
```

참고: 아래에 나열된 명령에 대한 자세한 내용은 [Front Door용 Azure CLI 참조](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest)를 확인하세요.

## <a name="create-an-azure-front-door-afd-resource"></a>AFD(Azure Front Door) 리소스 만들기

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**: 백 엔드 주소는 보호하려는 애플리케이션의 FQDN(정규화된 도메인 이름) 이름입니다. 예: myapplication.contoso.com

**--accepted-protocols**: 허용되는 프로토콜은 AFD가 웹 애플리케이션에 지원할 모든 프로토콜을 지정합니다. 예: --accepted-protocols Http Https

**--name**: AFD 리소스의 이름을 지정합니다.

**--resource-group**: 이 AFD 리소스를 배치하려는 리소스 그룹입니다.  리소스 그룹에 대해 자세히 알아보려면 Azure에서 리소스 그룹 관리를 참조하세요.

이 명령을 성공적으로 실행하여 받은 응답에서 "hostName" 키를 찾아서 값을 적어둡니다. 이후 단계에서 필요합니다. hostName은 생성한 AFD 리소스의 DNS 이름입니다.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure Front Door 리소스에 사용할 Azure WAF 프로필 만들기

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Azure WAF 정책의 이름을 지정합니다.

--resource-group 이 WAF 리소스를 배치하려는 리소스 그룹입니다. 

위의 CLI 코드는 사용하도록 설정되고 방지 모드에 있는 WAF 정책을 만듭니다. 

참고: WAF를 검색 모드로 만들어서 악의적인 요청을 탐지하고 기록하는 방식을 관찰한 후에(차단하지 않음) 보호 모드로 변경할지 결정할 수 있습니다.

이 명령을 성공적으로 실행하여 받은 응답에서 "ID" 키를 찾아서 값을 적어둡니다. 이후 단계에서 필요합니다. ID 필드의 형식은 다음과 같습니다.

/subscriptions/**구독 ID**/resourcegroups/**리소스 그룹 이름**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF 정책 이름**

## <a name="add-managed-rulesets-to-this-waf-policy"></a>관리형 규칙 집합을 WAF 정책에 추가

WAF 정책에서 관리형 규칙 세트를 추가할 수 있습니다. 이것은 Microsoft가 구축하고 관리하는 일련의 규칙이며, 모든 종류의 위협으로부터 보호하는 기능이 기본으로 제공됩니다. 이 예제에서는 두 가지 규칙 집합 즉, (1) 일반적인 웹 위협으로부터 보호하는 기본 규칙 집합과 (2) 악성 봇으로부터 보호하는 봇 보호 규칙 집합을 추가합니다.

(1) 기본 규칙 집합 추가

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) 봇 관리자 규칙 집합 추가

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name Azure WAF 리소스에 지정한 이름입니다.

--resource-group 이 WAF 리소스를 배치한 리소스 그룹입니다.

## <a name="associate-the-waf-policy-with-the-afd-resource"></a>WAF 정책을 AFD 리소스와 연결

이 단계에서는 구축한 WAF 정책을 웹 애플리케이션 앞에 있는 AFD 리소스와 연결합니다.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name AFD 리소스에 지정한 이름입니다.

--resource-group Azure Front Door 리소스를 배치한 리소스 그룹입니다.

--set AFD 리소스와 연결된 frontendEndpoint에 대한 WebApplicationFirewallPolicyLink 특성을 새로 구축한 WAF 정책으로 업데이트합니다. WAF 정책의 ID는 위 2단계에서 받은 응답에서 찾을 수 있습니다.

참고: 위의 예는 사용자 지정 도메인을 사용하지 않는 경우에 해당합니다.

사용자 지정 도메인을 사용하여 웹 애플리케이션에 액세스하는 경우가 아니면 5단계를 건너뛸 수 있습니다. 이 경우에는 웹 애플리케이션으로 이동할 수 있도록 1단계에서 받은 호스트 이름을 최종 사용자에게 제공합니다

## <a name="configure-custom-domain-for-your-web-application"></a>웹 애플리케이션에 대한 사용자 지정 도메인 구성

처음에는 웹 애플리케이션의 사용자 지정 도메인 이름(고객이 애플리케이션을 참조하는 데 사용됨, 예: www.contoso.com)이 AFD가 도입되기 전에 실행하던 위치를 가리켰습니다. 애플리케이션 앞에 AFD+WAF를 추가하여 아키텍처를 변경한 후에는 사용자 지정 도메인에 해당하는 DNS 항목이 이제 AFD 리소스를 가리켜 야합니다. 이렇게 하려면 DNS 서버의 항목을 1단계에서 적어둔 AFD 호스트 이름에 다시 매핑하면 됩니다.

DNS 레코드를 업데이트하는 구체적인 단계는 DNS 서비스 공급자에 따라 다르지만 Azure DNS를 사용하여 DNS 이름을 호스트하는 경우에는 [DNS 레코드를 업데이트하는 단계](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli)에 대한 설명서를 참조하여 AFD hostName을 가리킬 수 있습니다. 

여기서 한 가지 중요한 점은, 사용자가 영역 루트(예: contoso.com)를 사용하여 웹 사이트로 이동하도록 하려면 Azure DNS 및 해당 [ALIAS 레코드 유형](https://docs.microsoft.com/azure/dns/dns-alias)을 사용하여 DNS 이름을 호스트해야 한다는 점입니다. 

또한 AFD 구성도 업데이트하여 [사용자 지정 도메인을 추가](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)해야 합니다. 그래야 AFD가 매핑을 이해할 수 있습니다.

마지막으로 사용자 지정 도메인을 사용하여 웹 애플리케이션에 연결하고 HTTPS 프로토콜을 사용하도록 설정하려면, [AFD에 사용자 지정 도메인 설정을 위한 인증서](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)가 있어야 합니다. 

## <a name="lock-down-your-web-application"></a>웹 애플리케이션 잠금

따를 수 있는 한 가지 모범 사례는 AFD 에지만 웹 애플리케이션과 통신할 수 있도록 하는 것입니다. 이 작업을 수행하면 아무도 AFD 보호를 우회하여 애플리케이션에 직접 액세스할 수 없습니다. 잠금 작업은 [AFD의 FAQ 섹션](https://docs.microsoft.com/azure/frontdoor/front-door-faq)을 방문하여 AFD만 액세스할 수 있도록 백 엔드를 잠그는 것과 관련된 질문을 참조하여 수행할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스가 더 이상 필요 없으면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용하여 리소스 그룹, Front Door, WAF 정책을 제거합니다.

```azurecli-interactive
  az group delete \
    --name <>
```
----name 이 자습서에서 배포된 모든 리소스의 리소스 그룹 이름입니다.

## <a name="next-steps"></a>다음 단계

Front Door의 문제를 해결하는 방법을 알아보려면 방법 가이드를 계속 살펴보세요.

> [!div class="nextstepaction"]
> [일반적인 라우팅 문제 해결](front-door-troubleshoot-routing.md)
