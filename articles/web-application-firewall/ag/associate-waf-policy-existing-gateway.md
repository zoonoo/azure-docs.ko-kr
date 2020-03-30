---
title: 웹 응용 프로그램 방화벽 정책을 기존 Azure 응용 프로그램 게이트웨이와 연결
description: 웹 응용 프로그램 방화벽 정책을 기존 Azure 응용 프로그램 게이트웨이와 연결하는 방법을 알아봅니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083901"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>WAF 정책을 기존 응용 프로그램 게이트웨이와 연결

Azure PowerShell을 사용하여 [WAF 정책을 만들](create-waf-policy-ag.md)수 있지만 이미 응용 프로그램 게이트웨이가 있고 WAF 정책을 연결하려는 경우만 사용할 수 있습니다. 이 문서에서는 이 작업을 수행합니다. WAF 정책을 만들고 기존 응용 프로그램 게이트웨이에 연결합니다. 

1. 응용 프로그램 게이트웨이 및 방화벽 정책을 가져옵니다. 기존 방화벽 정책이 없는 경우 2단계를 참조하세요. 

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
   > WAF 구성에서 WAF 정책으로 전환하기 위해 이 WAF 정책을 만드는 경우 정책은 이전 Config의 정확한 복사본이어야 합니다. 즉, 모든 제외, 사용자 지정 규칙, 사용 안 함 규칙 그룹 등은 WAF Config의 것과 동일해야 합니다.
3. (선택 사항) 필요에 맞게 WAF 정책을 구성할 수 있습니다. 여기에는 사용자 지정 규칙, 규칙/규칙 그룹 비활성화, 제외, 파일 업로드 제한 설정 등이 포함됩니다. 이 단계를 건너뛰면 모든 기본값이 선택됩니다. 
   
4. 정책을 저장하고 응용 프로그램 게이트웨이에 연결합니다. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>다음 단계
[맞춤 규칙에 대해 알아보세요.](configure-waf-custom-rules.md)
