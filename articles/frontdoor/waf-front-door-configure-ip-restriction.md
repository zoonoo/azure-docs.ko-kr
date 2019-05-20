---
title: Azure 프런트 도어에 대 한 웹 응용 프로그램 방화벽 규칙을 사용 하 여 IP 제한 규칙 구성
description: 기존 프런트 도어 끝점에 대 한 IP 주소 제한 WAF 규칙을 구성 하는 방법에 알아봅니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: b129579916330a34a2a78d98f2c7653f129d3319
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523702"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Azure 프런트 도어 (미리 보기)에 대 한 웹 응용 프로그램 방화벽 IP 제한 규칙 구성
 이 문서에서는 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 첫 번째 관문에 대 한 Azure 웹 응용 프로그램 방화벽 (WAF)의 IP 제한 규칙을 구성 하는 방법을 보여 줍니다.

IP 주소 기반 액세스 제어 규칙은 도메인 간 라우팅 (CIDR (Classless) 형태로 IP 주소 또는 IP 주소 범위 목록을 지정 하 여 웹 응용 프로그램에 대 한 액세스를 제어할 수 있도록 사용자 지정 WAF 규칙.

기본적으로 웹 응용 프로그램은 인터넷에서 액세스할 수 있습니다. 웹 응용 프로그램 클라이언트에만 목록에서 알려진된 IP 주소 또는 IP 주소 범위에 대 한 액세스를 제한 하려는 경우에 두 개의 IP 일치 규칙을 만들고 해야 합니다. 첫 번째 IP 일치 하는 규칙 일치 하는 값으로 IP 주소 목록을 포함 하 고 "허용"으로 동작을 설정 합니다. 낮은 우선 순위를 사용 하 여 두 번째 "All" 연산자를 사용 하 여 다른 모든 IP 주소를 차단 하 고 "블록" 동작을 설정 하는 것입니다. IP 제한 규칙을 적용 되 면이 허용 된 목록 이외의 주소에서 시작 된 모든 요청이 403 (금지 됨) 응답을 받습니다.  

> [!IMPORTANT]
> WAF IP 제한에 대 한 Azure 프런트 도어 기능은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="configure-waf-policy-with-azure-cli"></a>Azure CLI를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>필수 조건
IP 제한 정책을 구성 하려면 먼저 CLI 환경을 설정 하 고 첫 번째 관문 프로필을 만듭니다.

#### <a name="set-up-azure-cli-environment"></a>Azure CLI 환경 설정
1. 설치를 [Azure CLI](/cli/azure/install-azure-cli), 또는 Azure Cloud Shell을 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI 사전 설치 및 구성 계정으로 사용 하 여 포함 합니다. 선택 합니다 **사용해** 단추 CLI에서 다음 명령입니다. 선택 **사용해** 사용 하 여 Azure 계정에 로그인 할 수 있는 Cloud Shell을 호출 합니다. Cloud shell 세션을 시작 되 면 입력 `az extension add --name front-door` 프런트 도어 확장을 추가 합니다.
 2. CLI를 로컬로 bash에서를 사용 하는 경우 사용 하 여 Azure에 로그인 `az login`합니다.

#### <a name="create-front-door-profile"></a>첫 번째 관문 프로필 만들기
[빠른 시작: 첫 번째 관문 프로필 만들기](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

사용 하 여 WAF 정책 만들기는 [az network waf 정책 만들기](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) 명령입니다. 에 아래 예제에서는 정책 이름을 바꿀 *IPAllowPolicyExampleCLI* 고유한 정책 이름으로 합니다.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙 추가

사용자 지정 IP 액세스 제어 규칙을 사용 하 여 이전 단계에서 만든 WAF 정책에 추가 합니다 [az network waf 정책 사용자 지정 규칙 만들기](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) 명령입니다. 

에 아래 예제:
-  바꿉니다 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.
-  대체 *ip 주소-범위 1*하십시오 *ip 주소-범위 2* 고유한 범위를 사용 하 여 합니다.

먼저 IP를 만들 지정 된 주소에 대 한 규칙을 허용 합니다.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
다음으로, 이전 IP 허용 규칙 보다 낮은 우선 순위의 블록 모든 IP 규칙을 만듭니다. 대체는 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>WAF 정책 ID를 찾으려면
사용 하 여 WAF 정책의 ID를 찾을 합니다 [az network waf 정책 표시](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) 명령입니다. 대체는 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>첫 번째 관문 프런트 엔드 호스트에 링크 WAF 정책

설정 첫 번째 관문 *WebApplicationFirewallPolicyLink* 정책 ID 사용 하 여 ID를 [az 네트워크 프런트 도어 업데이트](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) 명령입니다. 대체는 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예제에서는 WAF 정책 FrontendEndpoints [0]에 적용 됩니다. WAF 정책 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> 설정 해야 합니다 **WebApplicationFirewallPolicyLink** 프런트 엔드를 프런트 도어 WAF 정책을 연결할 속성을 한 번입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.

## <a name="configure-waf-policy-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>필수 조건
IP 제한 정책을 구성 하려면 먼저 PowerShell 환경을 설정 하 고 첫 번째 관문 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. Azure 자격 증명을 사용 하 여 로그인 페이지의 지침을 따르고 Az PowerShell 모듈을 설치 합니다.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인에 대 한 대화형 대화 상자를 사용 하 여 Azure에 연결
```
Connect-AzAccount

```
첫 번째 관문 모듈을 설치 하기 전에 현재 버전의 PowerShellGet 설치 했는지 확인 합니다. 아래 명령과 reopen 실행 PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기
[빠른 시작: 첫 번째 관문 프로필 만들기](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>IP 일치 조건 정의
사용 합니다 [새로 만들기-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) IP 일치 상태를 정의 하는 명령입니다. 에 아래 예제에서는 대체 *ip 주소-범위 1*를 *ip 주소-범위 2* 고유한 범위를 사용 하 여 합니다.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
IP와 일치 하는 모든 조건 규칙 만들기
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>사용자 지정 만들기 규칙을 허용 하는 IP
   사용 합니다 [새로 만들기-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 동작을 정의 및 우선 순위를 설정 하는 명령을 합니다. 다음 예에서 목록과 일치 하는 클라이언트 Ip에서에서 요청이 허용 됩니다. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
이전 IP 허용 규칙 보다 우선 순위가 낮은 모든 IP 규칙 블록을 만듭니다.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>WAF 정책 구성
`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 다음으로, WAF 정책을 사용 하 여 IP 블록 규칙 구성 [새로 만들기-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)합니다.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>첫 번째 관문 프런트 엔드 호스트에 링크 WAF 정책

기존 프런트 도어 프런트 엔드 호스트에 WAF 정책 개체에 연결 하 고 첫 번째 관문 속성을 업데이트 합니다. 먼저 사용 하 여 첫 번째 관문 개체를 검색할 [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)합니다. 그런 다음 프런트 엔드를 설정 *WebApplicationFirewallPolicyLink* 공간의 resourceId로 속성을 *$IPAllowPolicyExamplePS* 사용 하 여 이전 단계에서 만든는 [집합-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령입니다.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예제에서는 WAF 정책 FrontendEndpoints [0]에 적용 됩니다. WAF 정책 프런트 엔드에 연결할 수 있습니다. 설정 해야 *WebApplicationFirewallPolicyLink* 프런트 엔드를 프런트 도어 WAF 정책을 연결할 속성을 한 번입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 WAF 정책 구성
사용자 지정 IP 제한 규칙을 사용 하 여 프런트 도어 및 WAF 정책을 만드는 템플릿을 보거나 [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)합니다.


## <a name="next-steps"></a>다음 단계

- 에 대해 알아봅니다 하는 방법 [첫 번째 관문 프로필을 만드는](quickstart-create-front-door.md)합니다.
