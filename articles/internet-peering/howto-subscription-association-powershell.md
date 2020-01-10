---
title: PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결
titleSuffix: Azure
description: PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e7239fdedafedc96a382de6c3c2f90b5da4df00c
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774251"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>PowerShell을 사용 하 여 피어 ASN을 Azure 구독에 연결

피어 링 요청을 제출 하기 전에 먼저 다음 단계를 사용 하 여 ASN을 Azure 구독과 연결 해야 합니다.

원한다 면 [포털](howto-subscription-association-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN을 만들어 Azure 구독과 ASN 연결

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="update-the-peer-information-associated-with-this-subscription"></a>이 구독과 연결 된 피어 정보 업데이트

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name은 리소스 이름에 해당 하며 선택 하는 모든 이름일 수 있습니다. 그러나-peerName은 회사의 이름에 해당 하며, PeeringDB 프로필에 최대한 가까이 있어야 합니다. -PeerName의 값은 문자 a-z, a-z 및 공백만 지원 합니다.

구독에는 여러 개의 ASNs가 있을 수 있습니다. 각 ASN에 대 한 피어 링 정보를 업데이트 합니다. "이름"이 각 ASN에 대해 고유한 지 확인 합니다.

피어는 [Peeringdb](https://www.peeringdb.com)에 완전 하 고 최신 프로필이 있어야 합니다. 등록 하는 동안이 정보를 사용 하 여 NOC 정보, 기술 연락처 정보 및 피어 링 기능에서의 현재 상태와 같은 피어의 세부 정보에 대 한 유효성을 검사 합니다.

위의 출력에서 **{subscriptionId}** 대신 실제 구독 ID가 표시 됩니다.

## <a name="view-status-of-a-peerasn"></a>PeerASN의 상태 보기

다음 명령을 사용 하 여 ASN 유효성 검사 상태를 확인 합니다.

```powershell
Get-AzPeerAsn
```

다음은 응답 예제입니다.
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> 상태를 피어 링 요청을 제출 하기 전에 ValidationState가 "승인 됨"으로 전환 될 때까지 기다립니다. 이 승인에는 최대 12 시간이 걸릴 수 있습니다.

## <a name="modify-peerasn"></a>PeerAsn 수정
언제 든 지 NOC 연락처 정보를 수정할 수 있습니다.

예제는 아래와 같습니다.

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn 삭제
PeerASN 삭제는 현재 지원 되지 않습니다. PeerASN을 삭제 해야 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하세요.

## <a name="next-steps"></a>다음 단계

* [직접 피어 링 만들기 또는 수정](howto-direct-powershell.md)
* [레거시 직접 피어 링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)
* [Exchange 피어 링 만들기 또는 수정](howto-exchange-powershell.md)
* [레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.
