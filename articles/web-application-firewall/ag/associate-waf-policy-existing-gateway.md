---
title: 기존 Azure 애플리케이션 게이트웨이에 웹 응용 프로그램 방화벽 정책 연결
description: 기존 Azure 애플리케이션 게이트웨이와 웹 응용 프로그램 방화벽 정책을 연결 하는 방법에 대해 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: b455849c889c463fbda305e690f998d58fab0d8f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516878"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>WAF 정책을 기존 Application Gateway에 연결

Azure PowerShell를 사용 하 여 [WAF 정책을 만들](create-waf-policy-ag.md)수 있지만 이미 Application Gateway 있고 waf 정책을 연결 하려고 할 수 있습니다. 이 문서에서는 다음과 같은 작업을 수행 합니다. WAF 정책을 만들어 이미 기존 Application Gateway에 연결 합니다. 

1. Application Gateway 및 방화벽 정책을 가져옵니다. 기존 방화벽 정책이 없는 경우 2 단계를 참조 하세요. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. 필드 방화벽 정책을 만듭니다.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Waf 구성에서 WAF 정책으로 전환 하기 위해이 WAF 정책을 만드는 경우 정책이 이전 구성의 정확한 복사본 이어야 합니다. 즉, 모든 제외, 사용자 지정 규칙, 사용 되지 않는 규칙 그룹 등은 WAF 구성에 있는 것과 정확히 일치 해야 합니다.
3. 필드 사용자의 요구에 맞게 WAF 정책을 구성할 수 있습니다. 여기에는 사용자 지정 규칙, 규칙/규칙 그룹 사용 안 함, 제외, 파일 업로드 제한 설정 등이 포함 됩니다. 이 단계를 건너뛰면 모든 기본값이 선택 됩니다. 
   
4. 정책을 저장 하 고 Application Gateway에 연결 합니다. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>다음 단계
[사용자 지정 규칙에 대해 알아봅니다.](/configure-waf-custom-rules.md)
