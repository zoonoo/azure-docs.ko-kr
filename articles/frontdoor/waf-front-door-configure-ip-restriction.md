---
title: Azure Front 도어 서비스에 대 한 웹 응용 프로그램 방화벽 규칙을 사용 하 여 IP 제한 규칙 구성
description: 기존 Azure Front 도어 서비스 끝점에 대 한 IP 주소를 제한 하도록 웹 응용 프로그램 방화벽 규칙을 구성 하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 611a52f43b5b0e076ae37df9df86479ec894c6f4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849160"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Azure Front 도어 서비스에 대 한 웹 응용 프로그램 방화벽을 사용 하 여 IP 제한 규칙 구성
이 문서에서는 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 Azure Front 도어 서비스의 WAF (웹 응용 프로그램 방화벽)에서 IP 제한 규칙을 구성 하는 방법을 보여 줍니다.

IP 주소 기반 액세스 제어 규칙은 웹 응용 프로그램에 대 한 액세스를 제어할 수 있는 사용자 지정 WAF 규칙입니다. 이를 위해 CIDR (클래스 간 라우팅) 형식으로 IP 주소 또는 ip 주소 범위 목록을 지정 합니다.

기본적으로 웹 응용 프로그램은 인터넷에서 액세스할 수 있습니다. 알려진 IP 주소 또는 IP 주소 범위 목록에서 클라이언트에 대 한 액세스를 제한 하려는 경우 두 개의 IP 일치 규칙을 만들어야 합니다. 첫 번째 IP 일치 규칙은 일치 하는 값으로 IP 주소 목록을 포함 하 고 작업을 **허용**으로 설정 합니다. 우선 순위가 낮은 두 번째는 **all** 연산자를 사용 하 여 다른 모든 IP 주소를 차단 하 고 작업을 **차단**으로 설정 합니다. IP 제한 규칙이 적용 된 후이 허용 목록 외부의 주소에서 발생 한 요청은 403 금지 된 응답을 수신 합니다.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>전제 조건
IP 제한 정책 구성을 시작 하기 전에 CLI 환경을 설정 하 고 Azure Front 도어 서비스 프로필을 만듭니다.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 환경 설정
1. [Azure CLI](/cli/azure/install-azure-cli)를 설치 하거나 Azure Cloud Shell를 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **사용해 보기** 단추를 선택한 다음, 열리는 Cloud Shell 세션에서 Azure 계정에 로그인 합니다. 세션이 시작 되 면를 입력 `az extension add --name front-door` 하 여 Azure Front 도어 서비스 확장을 추가 합니다.
 2. Bash에서 로컬로 CLI를 사용 하는 경우를 사용 `az login`하 여 Azure에 로그인 합니다.

#### <a name="create-an-azure-front-door-service-profile"></a>Azure Front 도어 서비스 프로필 만들기
퀵 스타트에 [설명 된 지침에 따라 Azure Front 도어 서비스 프로필을 만듭니다. 항상 사용 가능한 전역 웹 응용 프로그램](quickstart-create-front-door.md)에 대 한 프런트 도어를 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

[Az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) 명령을 사용 하 여 waf 정책을 만듭니다. 다음 예에서는 정책 이름 *IPAllowPolicyExampleCLI* 을 고유한 정책 이름으로 바꿉니다.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙 추가

[Az network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) 명령을 사용 하 여 방금 만든 waf 정책에 대 한 사용자 지정 IP 액세스 제어 규칙을 추가 합니다.

다음 예제에서:
-  *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.
-  *Ip 주소-1*, *ip 주소-2* 를 사용자 고유의 범위로 바꿉니다.

먼저 지정 된 주소에 대 한 IP 허용 규칙을 만듭니다.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
다음으로, 이전 **허용** 규칙 보다 우선 순위가 낮은 **모든** 규칙을 만듭니다. 다시, 다음 예제의 *IPAllowPolicyExampleCLI* 을 앞에서 만든 고유한 정책으로 바꿉니다.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>WAF 정책의 ID 찾기 
[Az network waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) 명령을 사용 하 여 WAF 정책의 ID를 찾습니다. 다음 예제의 *IPAllowPolicyExampleCLI* 을 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Azure Front 도어 서비스 프런트 엔드 호스트에 WAF 정책 연결

[Az network front-도어 update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) 명령을 사용 하 여 Azure Front 도어 서비스 *WebApplicationFirewallPolicyLink* ID를 정책 id로 설정 합니다. *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예에서 WAF 정책은 **FrontendEndpoints [0]** 에 적용 됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> WAF 정책을 Azure Front 도어 서비스 프런트 엔드에 연결 하려면 **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정 해야 합니다. 후속 정책 업데이트는 자동으로 프런트 엔드에 적용 됩니다.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>필수 구성 요소
IP 제한 정책 구성을 시작 하기 전에 PowerShell 환경을 설정 하 고 Azure Front 도어 서비스 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용 하 여 Azure 리소스를 관리 하는 cmdlet 집합을 제공 합니다.

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용 하 여 PowerShell에 로그인 한 다음 Az module을 설치 합니다.

1. 다음 명령을 사용 하 여 Azure에 연결한 다음 대화형 대화 상자를 사용 하 여 로그인 합니다.
    ```
    Connect-AzAccount
    ```
 2. Azure Front 도어 서비스 모듈을 설치 하기 전에 현재 버전의 PowerShellGet 모듈이 설치 되어 있는지 확인 합니다. 다음 명령을 실행 하 고 PowerShell을 다시 엽니다.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 다음 명령을 사용 하 여 FrontDoor 모듈을 설치 합니다. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Azure Front 도어 서비스 프로필 만들기
퀵 스타트에 [설명 된 지침에 따라 Azure Front 도어 서비스 프로필을 만듭니다. 항상 사용 가능한 전역 웹 응용 프로그램](quickstart-create-front-door.md)에 대 한 프런트 도어를 만듭니다.

### <a name="define-an-ip-match-condition"></a>IP 일치 조건 정의
[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 명령을 사용 하 여 IP 일치 조건을 정의 합니다.
다음 예제에서는 *ip 주소*범위-1, *ip 주소-2* 를 사용자의 범위를 바꿉니다.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
다음 명령을 사용 하 여 IP *일치 모든 조건* 규칙을 만듭니다.
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>사용자 지정 IP 허용 규칙 만들기

[AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 명령을 사용 하 여 작업을 정의 하 고 우선 순위를 설정 합니다. 다음 예제에서는 목록과 일치 하는 클라이언트 Ip의 요청이 허용 됩니다.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
이전 IP **허용** 규칙 보다 우선 순위가 낮은 **모든** 규칙을 만듭니다.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>WAF 정책 구성
을 사용 `Get-AzResourceGroup`하 여 Azure Front 도어 서비스 프로필이 포함 된 리소스 그룹의 이름을 찾습니다. 그런 다음 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용 하 여 IP **블록 모든** 규칙을 사용 하 여 waf 정책을 구성 합니다.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Azure Front 도어 서비스 프런트 엔드 호스트에 WAF 정책 연결

WAF 정책 개체를 기존 프런트 엔드 호스트에 연결 하 고 Azure Front 도어 서비스 속성을 업데이트 합니다. 먼저 [AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용 하 여 Azure Front 도어 서비스 개체를 검색 합니다. 그런 다음 **WebApplicationFirewallPolicyLink** 속성을 [AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용 하 여 이전 단계에서 만든 *$IPAllowPolicyExamplePS*의 리소스 ID로 설정 합니다.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예에서 WAF 정책은 **FrontendEndpoints [0]** 에 적용 됩니다. WAF 정책을 프런트 엔드에 연결할 수 있습니다. WAF 정책을 Azure Front 도어 서비스 프런트 엔드에 연결 하려면 **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정 해야 합니다. 후속 정책 업데이트는 자동으로 프런트 엔드에 적용 됩니다.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>리소스 관리자 템플릿으로 WAF 정책 구성
사용자 지정 IP 제한 규칙을 사용 하 여 Azure Front 도어 서비스 정책과 WAF 정책을 만드는 템플릿을 보려면 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)로 이동 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Front 도어 서비스 프로필을 만드는](quickstart-create-front-door.md)방법에 대해 알아봅니다.
