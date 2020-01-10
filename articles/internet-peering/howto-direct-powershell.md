---
title: PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정
titleSuffix: Azure
description: PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774238"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>PowerShell을 사용 하 여 직접 피어 링 만들기 또는 수정

이 문서에서는 PowerShell cmdlet 및 리소스 관리자 배포 모델을 사용 하 여 Microsoft 직접 피어 링을 만드는 방법을 설명 합니다. 또한이 문서에서는 리소스의 상태를 확인 하거나, 업데이트 하거나, 삭제 하 고 프로 비전을 해제 하는 방법을 보여 줍니다.

원한다 면 [포털](howto-direct-portal.md)을 사용 하 여이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작 하기 전에 [필수 조건](prerequisites.md) 및 [직접 피어 링 연습](walkthrough-direct-all.md) 을 검토 합니다.
* Azure 리소스로 변환 되지 않은 Microsoft와 직접 피어 링이 이미 있는 경우 [PowerShell을 사용 하 여 레거시 직접 피어 링을 azure 리소스로 변환](howto-legacy-direct-powershell.md) 을 참조 하세요.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>직접 피어 링 만들기 및 프로 비전

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>직접 피어 링에 대해 지원 되는 피어 링 위치 목록 가져오기
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>직접 피어 링 만들기
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>직접 피어 링 확인
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>직접 피어 링 수정
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>직접 피어 링 프로 비전 해제
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용 하 여 Exchange 피어 링을 만들거나 수정](howto-exchange-powershell.md)합니다.
* [PowerShell을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)합니다.

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

자세한 내용은 [인터넷 피어 링 faq](faqs.md) (영문)를 참조 하세요.
