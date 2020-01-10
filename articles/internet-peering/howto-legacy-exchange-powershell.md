---
title: PowerShell을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환
titleSuffix: Azure
description: PowerShell을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775395"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>PowerShell을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환

이 문서에서는 PowerShell cmdlet을 사용 하 여 기존 레거시 Exchange 피어 링을 Azure 리소스로 변환 하는 방법을 설명 합니다.

원한다 면 [포털](howto-legacy-exchange-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 구성 요소](prerequisites.md) 및 [Exchange 피어 링 연습](walkthrough-exchange-all.md) 을 검토 하십시오.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>레거시 Exchange 피어 링을 Azure 리소스로 변환

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>변환을 위해 레거시 Exchange 피어 링 가져오기
다음은 시애틀 피어 링 위치에서 레거시 Exchange 피어 링을 가져오는 예제입니다.

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

### <a name="convert-legacy-peering"></a>레거시 피어 링 변환
다음 명령을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환할 수 있습니다.

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> 레거시 피어 링을 azure 리소스로 변환할 때 수정은 지원 되지 않습니다 &nbsp;

종단 간 프로 비전이 성공적으로 완료 된 경우의 예제 응답은 다음과 같습니다.

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
자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용 하 여 Exchange 피어 링 만들기 또는 수정](howto-exchange-powershell.md)
