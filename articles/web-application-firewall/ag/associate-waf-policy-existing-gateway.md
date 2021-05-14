---
title: 기존 Azure Application Gateway에 Web Application Firewall 정책 연결
description: 기존 Azure Application Gateway에 Web Application Firewall 정책을 연결하는 방법에 대해 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74083901"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>기존 Application Gateway에 WAF 정책 연결

Azure PowerShell을 사용하여 [WAF 정책을 만들 수 있지만](create-waf-policy-ag.md) 이미 Application Gateway가 있을 수 있고 WAF 정책만 연결하고 싶을 수 있습니다. 이 문서에서는 WAF 정책을 만들어 기존 Application Gateway에 연결합니다. 

1. Application Gateway 및 방화벽 정책을 가져옵니다. 기존 방화벽 정책이 없는 경우 2단계를 참조하세요. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (선택 사항) 방화벽 정책을 만듭니다.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > WAF 구성에서 WAF 정책으로 전환하는 이 WAF 정책을 만드는 경우 정책이 기존 구성과 정확히 일치해야 합니다. 즉, 모든 제외, 사용자 지정 규칙, 사용할 수 없는 규칙 그룹 등은 WAF 구성에 있는 것과 정확히 일치해야 합니다.
3. (선택 사항) 사용자의 요구에 맞게 WAF 정책을 구성할 수 있습니다. 이 구성에 사용자 지정 규칙, 규칙/규칙 그룹, 제외 비활성화, 파일 업로드 제한 설정 등이 포함됩니다. 이 단계를 건너뛰면 모두 기본값이 선택됩니다. 
   
4. 정책을 저장하고 Application Gateway에 연결합니다. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>다음 단계
[사용자 지정 역할에 대한 자세한 정보.](configure-waf-custom-rules.md)
