---
title: PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정
titleSuffix: Azure
description: PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071757"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정

이 문서에서는 PowerShell cmdlet 및 Resource Manager 배포 모델을 사용하여 Microsoft Exchange 피어링을 만드는 방법을 설명합니다. 리소스의 상태를 확인하거나 리소스를 업데이트하거나 리소스를 삭제하고 프로비저닝 해제하는 방법도 나와 있습니다.

원할 경우 Azure [Portal](howto-exchange-portal.md)을 통해 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 요소](prerequisites.md) 및 [Exchange 피어링 연습](walkthrough-exchange-all.md)을 검토합니다.
* Exchange를 Microsoft와 이미 피어링했으며 Azure 리소스로 아직 변환하지 않은 경우 [PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)을 참조하세요.

### <a name="work-with-azure-powershell"></a>Azure PowerShell을 사용하여 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange 피어링 만들기 및 프로비저닝

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Exchange 피어링이 지원되는 피어링 위치 목록 가져오기
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange 피어링 만들기
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange 피어링 가져오기
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

자세한 내용은 [인터넷 피어링 FAQ](faqs.md)를 참조하세요.
