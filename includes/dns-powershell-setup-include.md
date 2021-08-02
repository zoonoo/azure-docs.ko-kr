---
title: Azure DNS용 PowerShell에 대한 파일 포함
description: Azure DNS용 PowerShell에 대한 파일 포함
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 04/28//2021
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS, devx-track-azurepowershell
ms.openlocfilehash: 2454ab0a95e8f69fb72e85b8fad083793e00735e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721053"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Azure DNS를 위한 Azure PowerShell 설정

### <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

구성을 시작하기 전에 다음 항목이 있는지 확인합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

### <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

PowerShell 콘솔을 열고 계정에 연결합니다. 자세한 내용은 [Azure PowerShell로 로그인](/powershell/azure/azurerm/authenticate-azureps)을 참조하세요.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-the-subscription"></a>구독 선택
 
계정에 대한 구독을 확인합니다.

```azurepowershell-interactive
Get-AzSubscription
```

사용할 Azure 구독을 선택합니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 그러나 모든 DNS 리소스는 국가별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure DNS에 영향을 주지 않습니다.

기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛸 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup -Name MyDNSResourceGroup -location "West US"
```
