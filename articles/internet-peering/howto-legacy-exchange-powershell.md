---
title: PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환
titleSuffix: Azure
description: PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590208"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환

이 문서에서는 PowerShell cmdlets을 사용하여 기존 레거시 Exchange 피어링을 Azure 리소스로 변환하는 방법을 설명합니다.

원할 경우 Azure [Portal](howto-legacy-exchange-portal.md)을 통해 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 요소](prerequisites.md) 및 [Exchange 피어링 연습](walkthrough-exchange-all.md)을 검토합니다.

### <a name="work-with-azure-powershell"></a>Azure PowerShell을 사용하여 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>레거시 Exchange 피어링을 Azure 리소스로 변환

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>변환을 위해 레거시 Exchange 피어링 가져오기
이 예제에서는 Seattle 피어링 위치에서 레거시 Exchange 피어링을 가져오는 방법을 설명합니다.

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

응답은 다음 예제와 유사합니다.
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>레거시 피어링 변환
다음 명령을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환할 수 있습니다.

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> 레거시 피어링을 Azure 리소스로 변환하면 수정이 지원되지 않습니다.
&nbsp;

이 예제 응답에서는 엔드투엔드 프로비저닝이 성공적으로 완료된 시기를 보여줍니다.

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```
자세한 내용은 [인터넷 피어링 FAQ](faqs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-powershell.md)
