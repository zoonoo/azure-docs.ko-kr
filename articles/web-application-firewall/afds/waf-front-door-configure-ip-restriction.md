---
title: Azure 정문 문에 대한 IP 제한 WAF 규칙 구성
description: 기존 Azure 정문 끝점에 대한 IP 주소를 제한하도록 웹 응용 프로그램 방화벽 규칙을 구성하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336099"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Azure 정문용 웹 응용 프로그램 방화벽으로 IP 제한 규칙 구성

이 문서에서는 Azure 포털, Azure CLI, Azure PowerShell 또는 Azure 리소스 관리자 템플릿을 사용하여 Azure 정문에 대한 WAF(웹 응용 프로그램 방화벽)에서 IP 제한 규칙을 구성하는 방법을 보여 주며, 이 문서에서는

IP 주소 기반 액세스 제어 규칙은 웹 응용 프로그램에 대한 액세스를 제어할 수 있는 사용자 지정 WAF 규칙입니다. 이를 위해 클래스리스 도메인 라우팅(CIDR) 형식의 IP 주소 또는 IP 주소 범위 목록을 지정합니다.

기본적으로 웹 응용 프로그램은 인터넷에서 액세스할 수 있습니다. 알려진 IP 주소 또는 IP 주소 범위 목록에서 클라이언트에 대한 액세스를 제한하려는 경우 IP 주소 목록을 일치하는 값으로 포함하는 IP 일치 규칙을 만들고 연산자를 "Not"(부정은 true)으로 설정하고 **차단작업을**할 수 있습니다. IP 제한 규칙이 적용된 후 허용된 목록 외부의 주소에서 시작된 요청은 403 금지된 응답을 받습니다.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Azure 포털을 통해 WAF 정책 구성

### <a name="prerequisites"></a>사전 요구 사항

빠른 시작: 가용성이 높은 글로벌 [웹 응용 프로그램에 대한 정문 만들기에](../../frontdoor/quickstart-create-front-door.md)설명된 지침에 따라 Azure 정문 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

1. Azure 포털에서 **리소스 만들기를**선택하고 검색 상자에 **웹 응용 프로그램 방화벽을** 입력한 다음 **WAF(웹 응용 프로그램 방화벽)를**선택합니다.
2. **만들기**를 선택합니다.
3. **WAF 정책 만들기** 페이지에서 다음 값을 사용하여 **기본 탭을 완료합니다.**
   
   |설정  |값  |
   |---------|---------|
   |에 대 한 정책     |글로벌 WAF (정문)|
   |Subscription     |구독 선택|
   |Resource group     |정문이 있는 리소스 그룹을 선택합니다.|
   |정책 이름     |정책의 이름 입력|
   |정책 상태     |사용|

   **다음: 정책 설정** 선택

1. 정책 **설정** 탭에서 **예방**을 선택합니다. 블록 **응답 본문에**대해 *차단된 유형!* 따라서 사용자 지정 규칙이 적용되는 지 확인할 수 있습니다.
2. **다음: 관리되는 규칙을 선택합니다.**
3. **다음: 사용자 지정 규칙을**선택합니다.
4. **사용자 지정 규칙 추가를 선택합니다.**
5. 사용자 **지정 규칙 추가** 페이지에서 다음 테스트 값을 사용하여 사용자 지정 규칙을 만듭니다.

   |설정  |값  |
   |---------|---------|
   |사용자 지정 규칙 이름     |FdWafCustRule|
   |상태     |사용|
   |규칙 유형     |일치|
   |우선 순위    |100|
   |일치 유형     |IP 주소|
   |변수 일치|리모트 애더|
   |작업(Operation)|포함하지 않음|
   |IP 주소 또는 범위|10.10.10.0/24|
   |작업|트래픽 거부|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="사용자 지정 규칙":::

   **추가**를 선택합니다.
6. **다음: 연결.**
7. **프런트 엔드 호스트 추가를**선택합니다.
8. **프런트 엔드 호스트의**경우 프런트 엔드 호스트를 선택하고 **추가를**선택합니다.
9. **검토 + 만들기를**선택합니다.
10. 정책 유효성 검사가 통과되면 **에서 만들기를**선택합니다.

### <a name="test-your-waf-policy"></a>WAF 정책 테스트

1. WAF 정책 배포가 완료되면 프런트 도어 프런트 엔드 호스트 이름으로 검색합니다.
2. 사용자 지정 차단 메시지가 표시됩니다.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 규칙 테스트":::

   > [!NOTE]
   > 개인 IP 주소는 규칙이 트리거될 수 있도록 사용자 지정 규칙에서 의도적으로 사용되었습니다. 실제 배포에서는 특정 상황에 대한 IP 주소를 사용하여 *허용* 및 *거부* 규칙을 만듭니다.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI로 WAF 정책 구성

### <a name="prerequisites"></a>사전 요구 사항
IP 제한 정책을 구성하기 전에 CLI 환경을 설정하고 Azure 정문 프로필을 만듭니다.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 환경 설정
1. Azure [CLI를](/cli/azure/install-azure-cli)설치하거나 Azure 클라우드 셸을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음에 있는 CLI 명령에서 **시도** 단추를 선택한 다음 열리는 Cloud Shell 세션에서 Azure 계정에 로그인합니다. 세션이 시작된 후 `az extension add --name front-door` 입력하여 Azure 정문 확장을 추가합니다.
 2. Bash에서 로컬로 CLI를 사용하는 경우 을 사용하여 `az login`Azure에 로그인합니다.

#### <a name="create-an-azure-front-door-profile"></a>Azure 정문 프로필 만들기
빠른 시작: 가용성이 높은 글로벌 [웹 응용 프로그램에 대한 정문 만들기에](../../frontdoor/quickstart-create-front-door.md)설명된 지침에 따라 Azure 정문 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

[az 네트워크 정문 waf 정책 만들기](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) 명령을 사용하여 WAF 정책을 만듭니다. 다음 예제에서는 정책 이름 *IPAllowPolicyExampleCLI를* 고유한 정책 이름으로 바꿉니다.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙 추가

az [네트워크 전면 문 waf 정책 사용자 지정 규칙 만들기](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) 명령을 사용하여 방금 만든 WAF 정책에 대한 사용자 지정 IP 액세스 제어 규칙을 추가합니다.

다음 예제에서는 다음과 같은
-  *IPAllowPolicyExampleCLI를* 이전에 만든 고유 정책으로 바꿉니다.
-  *IP 주소 범위-1,* *ip-주소 범위-2를* 고유한 범위로 바꿉니다.

먼저 이전 단계에서 만든 정책에 대한 IP 허용 규칙을 만듭니다. 
> [!NOTE]
> **--지연은** 다음 단계에서 추가할 일치 조건이 있어야 하기 때문에 필요합니다.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
다음으로 규칙에 일치 조건을 추가합니다.

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF 정책의 ID 찾기 
[az 네트워크 전면 문 waf 정책 표시](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) 명령을 사용하여 WAF 정책의 ID를 찾습니다. 다음 예제에서 *IPAllowPolicyExampleCLI를* 이전에 만든 고유 정책으로 바꿉니다.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF 정책을 Azure 정문 프런트 엔드 호스트에 연결

[az 네트워크 정문 업데이트](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) 명령을 사용하여 Azure 정문 *웹응용방화벽정책링크* ID를 정책 ID로 설정합니다. *IPAllowPolicyExampleCLI를* 이전에 만든 고유 정책으로 바꿉니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예제에서는 WAF 정책이 **프런트엔드엔드포인트[0]에**적용됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> WAF 정책을 Azure 정문 프런트 엔드에 연결하려면 **WebApplicationFirewallPolicy** 속성을 한 번만 설정해야 합니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell을 통해 WAF 정책 구성

### <a name="prerequisites"></a>사전 요구 사항
IP 제한 정책을 구성하기 전에 PowerShell 환경을 설정하고 Azure 정문 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스 관리자 모델을 사용하여 [Azure 리소스를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 관리하는 cmdlet 집합을 제공합니다.

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용하여 PowerShell에 로그인한 다음 Az 모듈을 설치합니다.

1. 다음 명령을 사용하여 Azure에 연결한 다음 대화형 대화 상자를 사용하여 로그인합니다.
    ```
    Connect-AzAccount
    ```
 2. Azure 정문 모듈을 설치하기 전에 PowerShellGet 모듈의 현재 버전이 설치되어 있는지 확인합니다. 다음 명령을 실행한 다음 PowerShell을 다시 엽니다.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 다음 명령을 사용하여 Az.FrontDoor 모듈을 설치합니다. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure 정문 프로필 만들기
빠른 시작: 가용성이 높은 글로벌 [웹 응용 프로그램에 대한 정문 만들기에](../../frontdoor/quickstart-create-front-door.md)설명된 지침에 따라 Azure 정문 프로필을 만듭니다.

### <a name="define-an-ip-match-condition"></a>IP 일치 조건 정의
새 [AzFrontDoorWaf일치조건대상](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 명령을 사용하여 IP 일치 조건을 정의합니다.
다음 예제에서는 *ip-주소 범위-1,* *ip-주소 범위-2를* 고유한 범위로 바꿉니다.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>사용자 지정 IP 허용 규칙 만들기

[New-AzFrontDoorWaf CustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 명령을 사용하여 작업을 정의하고 우선 순위를 설정합니다. 다음 예제에서는 목록과 일치하는 클라이언트 IP가 아닌 요청이 차단됩니다.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF 정책 구성
을 사용하여 `Get-AzResourceGroup`Azure 정문 프로필을 포함하는 리소스 그룹의 이름을 찾습니다. 다음으로 [New-AzFrontDoorWaf Policy를](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)사용하여 IP 규칙으로 WAF 정책을 구성합니다.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF 정책을 Azure 정문 프런트 엔드 호스트에 연결

WAF 정책 개체를 기존 프런트 엔드 호스트에 연결하고 Azure 정문 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용하여 Azure 정문 개체를 검색합니다. 다음으로 [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용하여 이전 단계에서 만든 *$IPAllowPolicyExamplePS*리소스 ID로 **WebApplicationFirewallPolicy** 속성을 설정합니다.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예제에서는 WAF 정책이 **프런트엔드엔드포인트[0]에**적용됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다. WAF 정책을 Azure 정문 프런트 엔드에 연결하려면 **WebApplicationFirewallPolicy** 속성을 한 번만 설정해야 합니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>리소스 관리자 템플릿으로 WAF 정책 구성
Azure 정문 정책을 만드는 템플릿과 사용자 지정 IP 제한 규칙이 있는 WAF 정책을 보려면 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)로 이동하십시오.


## <a name="next-steps"></a>다음 단계

- [Azure 정문 프로필을 만드는](../../frontdoor/quickstart-create-front-door.md)방법에 대해 알아봅니다.
