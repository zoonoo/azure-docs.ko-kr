---
title: '실패 한 회로를 다시 설정-Express 경로: PowerShell: Azure | Microsoft Docs'
description: 이 문서는 실패한 상태의 ExpressRoute 회로를 다시 설정하는 데 도움이 됩니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 84c8275b7a7257530a735e8612047ef42e2f8a7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011342"
---
# <a name="reset-a-failed-expressroute-circuit"></a>실패한 Azure ExpressRoute 회로 다시 설정

Express 경로 회로에 대 한 작업이 성공적으로 완료 되지 않으면 회로는 ' 실패 ' 상태로 전환 될 수 있습니다. 이 문서는 실패 한 Azure Express 경로 회로를 다시 설정 하는 데 도움이 됩니다.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>회로 다시 설정

1. 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/install-az-ps)을 참조하세요.

2. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. 다음 명령을 실행하여 실패한 상태의 회로를 다시 설정합니다.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

회로가 이제 정상이 됩니다. 회로가 여전이 실패한 상태이면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)으로 지원 티켓을 엽니다.

## <a name="next-steps"></a>다음 단계

여전히 문제가 발생 하는 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 엽니다.
