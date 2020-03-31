---
title: PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정
titleSuffix: Azure
description: PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774407"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정

이 문서에서는 PowerShell cmdlet 및 리소스 관리자 배포 모델을 사용하여 Microsoft Exchange 피어링을 만드는 방법에 대해 설명합니다. 이 문서에서는 리소스의 상태를 확인하거나, 업데이트하거나, 삭제하고 프로비저닝해제하는 방법도 보여 주어 있습니다.

원하는 경우 [포털](howto-exchange-portal.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 프로그램](prerequisites.md) 및 Exchange [피어링 연습을](walkthrough-exchange-all.md) 검토합니다.
* Azure 리소스로 변환되지 않은 Microsoft와의 Exchange 피어링이 이미 있는 경우 [PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환하는](howto-legacy-exchange-powershell.md) 것을 참조하세요.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange 피어링 만들기 및 프로비전

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Exchange 피어링을 위해 지원되는 피어링 위치 목록 가져옵니다.
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange 피어링 만들기
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>교환 피어링 받기
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange 피어링 수정
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Exchange 피어링 프로비저닝 해제

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-powershell.md)
* [PowerShell을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

자세한 내용은 [인터넷 피어링 자주 묻는 질문(FAQ)을 방문하십시오.](faqs.md)
