---
title: Azure 전면 도어에 대 한 IP 제한 WAF 규칙 구성
description: 기존 Azure 프런트 도어 끝점에 대 한 IP 주소를 제한 하도록 웹 응용 프로그램 방화벽 규칙을 구성 하는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336099"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Azure Front 도어 용 웹 응용 프로그램 방화벽을 사용 하 여 IP 제한 규칙 구성

이 문서에서는 Azure Portal, Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 Azure Front 문에 대해 WAF (웹 응용 프로그램 방화벽)에서 IP 제한 규칙을 구성 하는 방법을 보여 줍니다.

IP 주소 기반 액세스 제어 규칙은 웹 응용 프로그램에 대 한 액세스를 제어할 수 있는 사용자 지정 WAF 규칙입니다. 이를 위해 CIDR (클래스 간 라우팅) 형식으로 IP 주소 또는 ip 주소 범위 목록을 지정 합니다.

기본적으로 웹 응용 프로그램은 인터넷에서 액세스할 수 있습니다. 알려진 IP 주소 또는 IP 주소 범위 목록에서 클라이언트에 대 한 액세스를 제한 하려는 경우 IP 주소 목록을 일치 하는 값으로 포함 하 고 연산자를 "Not" (부정이 true)로 설정 하 고 **차단할**작업을 설정 하는 ip 일치 규칙을 만들 수 있습니다. IP 제한 규칙이 적용 된 후이 허용 목록 외부의 주소에서 발생 한 요청은 403 금지 된 응답을 수신 합니다.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Azure Portal를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>전제 조건

[빠른 시작: 항상 사용 가능한 글로벌 웹 응용 프로그램에 대 한 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명 된 지침에 따라 Azure Front 도어 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

1. Azure Portal에서 **리소스 만들기**를 선택 하 고 검색 상자에 **웹 응용 프로그램 방화벽** 을 입력 한 다음 **Waf (웹 응용 프로그램 방화벽)** 를 선택 합니다.
2. **만들기**를 선택합니다.
3. **WAF 정책 만들기** 페이지에서 다음 값을 사용 하 여 **기본** 탭을 완료 합니다.
   
   |설정  |값  |
   |---------|---------|
   |정책     |전역 WAF (전방 도어)|
   |Subscription     |구독 선택|
   |Resource group     |프런트 도어가 있는 리소스 그룹을 선택 합니다.|
   |정책 이름     |정책 이름 입력|
   |정책 상태     |사용|

   **다음: 정책 설정** 을 선택 합니다.

1. **정책 설정** 탭에서 **방지**를 선택 합니다. 차단 **응답 본문**에 대해 차단 됨을 입력 합니다 *.* 따라서 사용자 지정 규칙이 적용 되는 것을 볼 수 있습니다.
2. **다음: 관리 되는 규칙**을 선택 합니다.
3. **다음: 사용자 지정 규칙**을 선택 합니다.
4. **사용자 지정 규칙 추가**를 선택 합니다.
5. **사용자 지정 규칙 추가** 페이지에서 다음 테스트 값을 사용 하 여 사용자 지정 규칙을 만듭니다.

   |설정  |값  |
   |---------|---------|
   |사용자 지정 규칙 이름     |FdWafCustRule|
   |상태     |사용|
   |규칙 유형     |일치|
   |우선 순위    |100|
   |일치 유형     |IP 주소|
   |일치 변수|RemoteAddr|
   |작업(Operation)|포함하지 않음|
   |IP 주소 또는 범위|10.10.10.0/24|
   |작업|트래픽 거부|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="사용자 지정 규칙":::

   **추가**를 선택합니다.
6. **다음: 연결**을 선택 합니다.
7. **프런트 엔드 호스트 추가**를 선택 합니다.
8. **프런트 엔드 호스트**에 대해 프런트 엔드 호스트를 선택 하 고 **추가**를 선택 합니다.
9. **검토 + 만들기**를 선택합니다.
10. 정책 유효성 검사를 통과 한 후 **만들기**를 선택 합니다.

### <a name="test-your-waf-policy"></a>WAF 정책 테스트

1. WAF 정책 배포가 완료 되 면 Front 도어 프런트 엔드 호스트 이름으로 이동 합니다.
2. 사용자 지정 블록 메시지가 표시 됩니다.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 규칙 테스트":::

   > [!NOTE]
   > 사용자 지정 규칙에서 의도적으로 개인 IP 주소를 사용 하 여 규칙이 트리거되도록 보장 했습니다. 실제 배포에서는 특정 상황에 대해 IP 주소를 사용 하 여 *허용* 및 *거부* 규칙을 만듭니다.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>전제 조건
IP 제한 정책 구성을 시작 하기 전에 CLI 환경을 설정 하 고 Azure Front 도어 프로필을 만듭니다.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 환경 설정
1. [Azure CLI](/cli/azure/install-azure-cli)를 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **사용해 보기** 단추를 선택한 다음, 열리는 Cloud Shell 세션에서 Azure 계정에 로그인 합니다. 세션이 시작 되 면를 입력 `az extension add --name front-door` 하 여 Azure Front 도어 확장을 추가 합니다.
 2. Bash에서 로컬로 CLI를 사용 하는 경우를 사용 `az login`하 여 Azure에 로그인 합니다.

#### <a name="create-an-azure-front-door-profile"></a>Azure Front 도어 프로필 만들기
[빠른 시작: 항상 사용 가능한 글로벌 웹 응용 프로그램에 대 한 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명 된 지침에 따라 Azure Front 도어 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

[Az network front 도어 waf-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) 명령을 사용 하 여 waf 정책을 만듭니다. 다음 예에서는 정책 이름 *IPAllowPolicyExampleCLI* 을 고유한 정책 이름으로 바꿉니다.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙 추가

[Az network front f-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) 명령을 사용 하 여 방금 만든 waf 정책에 대 한 사용자 지정 IP 액세스 제어 규칙을 추가 합니다.

다음 예제에서:
-  *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.
-  *Ip 주소-1*, *ip 주소-2* 를 사용자 고유의 범위로 바꿉니다.

먼저, 이전 단계에서 만든 정책에 대해 IP 허용 규칙을 만듭니다. 
> [!NOTE]
> **--** 다음 단계에서 규칙에 일치 조건을 추가 해야 하므로 연기할 필요가 있습니다.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
다음으로 규칙에 일치 조건을 추가 합니다.

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
[Az network front 도어 waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) 명령을 사용 하 여 WAF 정책의 ID를 찾습니다. 다음 예제의 *IPAllowPolicyExampleCLI* 을 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Azure Front 도어 프런트 엔드 호스트에 WAF 정책 연결

[Az network front-도어 update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) 명령을 사용 하 여 Azure Front 도어가 *WebApplicationFirewallPolicyLink* id를 정책 id로 설정 합니다. *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예에서 WAF 정책은 **FrontendEndpoints [0]** 에 적용 됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정 하 여 waf 정책을 Azure front 도어 프런트 엔드에 연결 해야 합니다. 후속 정책 업데이트는 자동으로 프런트 엔드에 적용 됩니다.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>전제 조건
IP 제한 정책 구성을 시작 하기 전에 PowerShell 환경을 설정 하 고 Azure Front 도어 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용 하 여 Azure 리소스를 관리 하는 cmdlet 집합을 제공 합니다.

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용 하 여 PowerShell에 로그인 한 다음 Az module을 설치 합니다.

1. 다음 명령을 사용 하 여 Azure에 연결한 다음 대화형 대화 상자를 사용 하 여 로그인 합니다.
    ```
    Connect-AzAccount
    ```
 2. Azure Front 도어 모듈을 설치 하기 전에 현재 버전의 PowerShellGet 모듈이 설치 되어 있는지 확인 합니다. 다음 명령을 실행 하 고 PowerShell을 다시 엽니다.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 다음 명령을 사용 하 여 FrontDoor 모듈을 설치 합니다. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure Front 도어 프로필 만들기
[빠른 시작: 항상 사용 가능한 글로벌 웹 응용 프로그램에 대 한 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명 된 지침에 따라 Azure Front 도어 프로필을 만듭니다.

### <a name="define-an-ip-match-condition"></a>IP 일치 조건 정의
[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 명령을 사용 하 여 IP 일치 조건을 정의 합니다.
다음 예제에서는 *ip 주소*범위-1, *ip 주소-2* 를 사용자의 범위를 바꿉니다.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>사용자 지정 IP 허용 규칙 만들기

[AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 명령을 사용 하 여 작업을 정의 하 고 우선 순위를 설정 합니다. 다음 예제에서는 목록과 일치 하는 클라이언트 Ip에서 가져오지 않은 요청이 차단 됩니다.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF 정책 구성
을 사용 `Get-AzResourceGroup`하 여 Azure Front 도어 프로필을 포함 하는 리소스 그룹의 이름을 찾습니다. 그런 다음 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용 하 여 IP 규칙으로 waf 정책을 구성 합니다.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Azure Front 도어 프런트 엔드 호스트에 WAF 정책 연결

WAF 정책 개체를 기존 프런트 엔드 호스트에 연결 하 고 Azure Front 도어 속성을 업데이트 합니다. 먼저 [AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용 하 여 Azure Front 도어 개체를 검색 합니다. 그런 다음 **WebApplicationFirewallPolicyLink** 속성을 [AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용 하 여 이전 단계에서 만든 *$IPAllowPolicyExamplePS*의 리소스 ID로 설정 합니다.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예에서 WAF 정책은 **FrontendEndpoints [0]** 에 적용 됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다. **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정 하 여 waf 정책을 Azure front 도어 프런트 엔드에 연결 해야 합니다. 후속 정책 업데이트는 자동으로 프런트 엔드에 적용 됩니다.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>리소스 관리자 템플릿으로 WAF 정책 구성
사용자 지정 IP 제한 규칙을 사용 하 여 Azure Front 도어 정책과 WAF 정책을 만드는 템플릿을 보려면 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)로 이동 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Front 도어 프로필을 만드는](../../frontdoor/quickstart-create-front-door.md)방법에 대해 알아봅니다.
