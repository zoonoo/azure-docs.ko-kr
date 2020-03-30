---
title: PowerShell을 사용하여 피어 ASN을 Azure 구독에 연결
titleSuffix: Azure
description: PowerShell을 사용하여 피어 ASN을 Azure 구독에 연결
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908981"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>PowerShell을 사용하여 피어 ASN을 Azure 구독에 연결

피어링 요청을 제출하기 전에 먼저 아래 단계를 사용하여 ASN을 Azure 구독과 연결해야 합니다.

원하는 경우 [포털](howto-subscription-association-portal.md)을 사용하여 이 가이드를 완료할 수 있습니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>피어ASN 생성하여 ASN을 Azure 구독과 연결합니다.

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>피어링 리소스 공급자등록
아래 명령을 사용하여 구독에서 피어링 리소스 공급자에 등록합니다. 이 작업을 실행하지 않으면 피어링을 설정하는 데 필요한 Azure 리소스에 액세스할 수 없습니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

아래 명령을 사용하여 등록 상태를 확인할 수 있습니다.
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> *등록대기상태가* 진행되기 전에 "등록"을 설정합니다. 명령을 실행한 후 5~30분 정도 걸릴 수 있습니다.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>이 구독과 연결된 피어 정보 업데이트

다음은 피어 정보를 업데이트하는 예제입니다.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> - 이름은 자원 이름에 해당하며 원하는 모든 것을 선택할 수 있습니다. 그러나 -peerName은 회사 이름에 해당하며 PeeringDB 프로필에 최대한 가까워야 합니다. -peerName에 대한 값은 문자 a-z, A-Z 및 공백만 지원합니다.

구독에는 여러 ASN이 있을 수 있습니다. 각 ASN에 대한 피어링 정보를 업데이트합니다. 각 ASN에 대해 "이름"이 고유한지 확인합니다.

[피어링DB.](https://www.peeringdb.com) 당사는 등록 중에 이 정보를 사용하여 NOC 정보, 기술 연락처 정보 및 피어링 시설 등에서피어의 존재와 같은 피어의 세부 정보를 검증합니다.

위의 출력에 **{subscriptionId}** 대신 실제 구독 ID가 표시됩니다.

## <a name="view-status-of-a-peerasn"></a>피어라젠의 상태 보기

아래 명령을 사용하여 ASN 유효성 검사 상태를 확인합니다.

```powershell
Get-AzPeerAsn
```

다음은 예제 응답입니다.
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
> 피어링 요청을 제출하기 전에 유효성 검사상태가 "승인됨"으로 바뀔 때까지 기다립니다. 이 승인에는 최대 12시간이 걸릴 수 있습니다.

## <a name="modify-peerasn"></a>피어라센 수정
언제든지 NOC 연락처 정보를 수정할 수 있습니다.

예제는 아래와 같습니다.

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>피어라센 삭제
피어ASN 삭제는 현재 지원되지 않습니다. PeerASN을 삭제해야 하는 경우 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오.

## <a name="next-steps"></a>다음 단계

* [직접 피어링 만들기 또는 수정](howto-direct-powershell.md)
* [레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)
* [Exchange 피어링 만들기 또는 수정](howto-exchange-powershell.md)
* [레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스

자세한 내용은 [인터넷 피어링 자주 묻는 질문(FAQ)을 방문하십시오.](faqs.md)
