---
title: PowerShell을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환
titleSuffix: Azure
description: PowerShell을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775018"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>PowerShell을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환

이 문서에서는 PowerShell cmdlet을 사용 하 여 기존 레거시 직접 피어 링을 Azure 리소스로 변환 하는 방법을 설명 합니다.

원한다 면 [포털](howto-legacy-direct-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 조건](prerequisites.md) 및 [직접 피어 링 연습](walkthrough-direct-all.md) 을 검토 합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>레거시 직접 피어 링을 Azure 리소스로 변환

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>변환에 대 한 레거시 직접 피어 링 가져오기
다음은 시애틀 피어 링 위치에서 레거시 직접 피어 링을 가져오는 예제입니다.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

다음은 응답 예제입니다.
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-legacy-direct-peering"></a>레거시 직접 피어 링 변환

&nbsp;
> [!IMPORTANT]
> 레거시 피어 링을 azure 리소스로 변환할 때 수정은 지원 되지 않습니다. &nbsp;

다음 명령을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환 합니다.

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

다음은 응답 예제입니다.

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용 하 여 직접 피어 링을 만들거나 수정](howto-direct-powershell.md)합니다.
