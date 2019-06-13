---
title: Azure 프런트 도어 서비스에 대 한 웹 응용 프로그램 방화벽 규칙을 사용 하 여 IP 제한 규칙 구성
description: 기존 Azure 프런트 도어 서비스 끝점에 대 한 IP 주소를 제한 하는 웹 응용 프로그램 방화벽 규칙을 구성 하는 방법에 알아봅니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743156"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Azure 프런트 도어 서비스에 대 한 웹 응용 프로그램 방화벽을 사용 하 여 IP 제한 규칙 구성
이 문서에서는 Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용 하 여 웹 응용 프로그램 방화벽 (WAF)의 Azure 프런트 도어 서비스에 대 한 IP 제한 규칙을 구성 하는 방법을 보여 줍니다.

IP 주소 기반 액세스 제어 규칙을 수 있는 사용자 지정 WAF 규칙은 웹 응용 프로그램에 대 한 액세스를 제어 합니다. 도메인 간 라우팅 (CIDR (Classless) 형식으로 IP 주소 또는 IP 주소 범위 목록을 지정 하 여 수행 합니다.

기본적으로 웹 응용 프로그램은 인터넷에서 액세스할 수 있습니다. 알려진된 IP 주소 또는 IP 주소 범위 목록에서 클라이언트에 대 한 액세스를 제한 하려는 경우에 두 개의 IP 일치 하는 규칙 만들어야 합니다. 첫 번째 IP 일치 하는 규칙의 IP 주소와 일치 하는 값 목록을 포함 하 고 작업을 설정 **허용**합니다. 하 고 두 번째 낮은 우선 순위를 사용 하 여 사용 하 여 다른 모든 IP 주소를 차단 합니다 **모든** 연산자와 작업을 설정 **블록**합니다. IP 제한 규칙을 적용 하면이 허용 된 목록 이외의 주소에서 발생 하는 요청에는 403 사용 권한 없음 응답을 수신 합니다.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI를 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>필수 조건
IP 제한 정책을 구성 하려면 먼저 CLI 환경을 설정 하 고 Azure 프런트 도어 서비스 프로필을 만듭니다.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 환경 설정
1. 설치를 [Azure CLI](/cli/azure/install-azure-cli), 또는 Azure Cloud Shell을 사용 합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 선택 합니다 **사용해** 따르고 다음 열리는 Cloud Shell 세션에서 Azure 계정에 로그인 하는 CLI 명령에는 단추입니다. 세션이 시작 된 후 입력 `az extension add --name front-door` Azure 프런트 도어 서비스 확장을 추가 합니다.
 2. Bash에 CLI를 로컬로 사용 중인 경우 Azure에 로그인 하 여 `az login`입니다.

#### <a name="create-an-azure-front-door-service-profile"></a>Azure 프런트 도어 서비스 프로필 만들기
에 설명 된 지침에 따라 Azure 프런트 도어 서비스 프로필을 만들기 [빠른 시작: 항상 사용 가능한 글로벌 웹 응용 프로그램을 첫 번째 관문 만들기](quickstart-create-front-door.md)합니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

사용 하 여 WAF 정책 만들기를 [az network waf 정책 만들기](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) 명령입니다. 같이 정책 이름을 바꿉니다는 예제의 *IPAllowPolicyExampleCLI* 고유한 정책 이름입니다.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙을 추가 합니다.

사용 하 여는 [az network waf 정책 사용자 지정 규칙 만들기](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) 명령을 사용자 지정 IP 액세스 제어 규칙을 WAF 정책에 대해 방금 만든에 추가 합니다.

다음 예제:
-  바꿉니다 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.
-  대체 *ip 주소-범위 1*하십시오 *ip 주소-범위 2* 고유한 범위를 사용 하 여 합니다.

먼저 IP를 만들 지정 된 주소에 대 한 규칙을 허용 합니다.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
다음으로 만듭니다는 **모두 차단** 이전 보다 더 낮은 우선 순위의 규칙 **허용** 규칙입니다. 이름으로 바꿀 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책 사용 하 여 다음 예제에서입니다.

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
사용 하 여 WAF 정책의 ID를 찾을 합니다 [az network waf 정책 표시](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) 명령입니다. 바꿉니다 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책 사용 하 여 다음 예제에서입니다.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF 정책을 Azure 프런트 도어 서비스 프런트 엔드 호스트에 연결

Azure 프런트 도어 서비스 설정 *WebApplicationFirewallPolicyLink* 정책 id를 사용 하 여 ID를 [az network 프런트 도어 업데이트](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) 명령입니다. 바꿉니다 *IPAllowPolicyExampleCLI* 이전에 만든 고유한 정책을 사용 하 여 합니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예제에서는 WAF 정책에 적용 됩니다 **FrontendEndpoints [0]** 합니다. WAF 정책에 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> 설정 해야 합니다 **WebApplicationFirewallPolicyLink** WAF 정책을 Azure 프런트 도어 서비스 프런트 엔드를 연결할 한 번만 속성입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 WAF 정책 구성

### <a name="prerequisites"></a>필수 조건
IP 제한 정책을 구성 하려면 먼저 PowerShell 환경을 설정 하 고 Azure 프런트 도어 서비스 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell을 사용 하는 cmdlet 집합을 제공 합니다 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) Azure 리소스를 관리 하는 것에 대 한 모델입니다.

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. Azure 자격 증명을 사용 하 여 PowerShell에 로그인 페이지의 지침을 따르고 Az 모듈을 설치 합니다.

1. 다음 명령을 사용 하 여 Azure에 연결한 후 대화형 대화 상자를 사용 하 여 로그인 합니다.
    ```
    Connect-AzAccount
    ```
 2. 모듈을 Azure 프런트 도어 서비스를 설치 하기 전에 현재 버전의 PowerShellGet 모듈이 설치 되어 있는지 확인 합니다. 다음 명령을 실행 하 고 PowerShell을 닫은 키를 누릅니다.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 다음 명령을 사용 하 여 Az.FrontDoor 모듈을 설치 합니다. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Azure 프런트 도어 서비스 프로필 만들기
에 설명 된 지침에 따라 Azure 프런트 도어 서비스 프로필을 만들기 [빠른 시작: 항상 사용 가능한 글로벌 웹 응용 프로그램을 첫 번째 관문 만들기](quickstart-create-front-door.md)합니다.

### <a name="define-an-ip-match-condition"></a>IP 일치 조건 정의
사용 합니다 [새로 만들기-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) IP 일치 상태를 정의 하는 명령입니다.
다음 예제에서는 바꿉니다 *ip 주소-범위 1*를 *ip 주소-범위 2* 고유한 범위를 사용 하 여 합니다.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
IP를 만듭니다 *모든 조건과 일치* 다음 명령을 사용 하 여 규칙:
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
만들기는 **모두 차단** 이전 IP 보다 낮은 우선 순위를 사용 하 여 규칙 **허용** 규칙입니다.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>WAF 정책 구성
사용 하 여 Azure 프런트 도어 서비스 프로필을 포함 하는 리소스 그룹의 이름을 찾으려면 `Get-AzResourceGroup`합니다. 그런 다음 IP를 사용 하 여 WAF 정책을 구성 **모두 차단** 사용 하 여 규칙 [새로 만들기-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)합니다.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF 정책을 Azure 프런트 도어 서비스 프런트 엔드 호스트에 연결

사이트를 기존에 있는 프런트 엔드 호스트 및 업데이트 Azure 프런트 도어 서비스 속성에 WAF 정책 개체를 연결 합니다. 먼저 사용 하 여 Azure 프런트 도어 서비스 개체를 검색할 [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)합니다. 다음으로 설정 합니다 **WebApplicationFirewallPolicyLink** 의 리소스 ID 속성 *$IPAllowPolicyExamplePS*사용 하 여 이전 단계에서 만든는 [집합-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)명령입니다.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예제에서는 WAF 정책에 적용 됩니다 **FrontendEndpoints [0]** 합니다. WAF 정책에 프런트 엔드에 연결할 수 있습니다. 설정 해야 합니다 **WebApplicationFirewallPolicyLink** WAF 정책을 Azure 프런트 도어 서비스 프런트 엔드를 연결할 한 번만 속성입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 WAF 정책 구성
로 이동 하는 Azure 프런트 도어 서비스 정책을 만들고 사용자 지정 IP 제한 규칙을 사용 하 여 WAF 정책 템플릿을 보려는 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)합니다.


## <a name="next-steps"></a>다음 단계

- 에 대해 알아봅니다 하는 방법 [Azure 프런트 도어 서비스 프로필을 만드는](quickstart-create-front-door.md)합니다.
