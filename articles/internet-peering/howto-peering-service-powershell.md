---
title: PowerShell을 사용하여 직접 피어링에서 피어링 서비스 활성화
titleSuffix: Azure
description: PowerShell을 사용하여 직접 피어링에서 피어링 서비스 활성화
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774186"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>PowerShell을 사용하여 직접 피어링에서 피어링 서비스 활성화

이 문서에서는 PowerShell cmdlet 및 리소스 관리자 배포 모델을 사용하여 직접 피어링에서 [피어링 서비스를](overview-peering-service.md) 사용하도록 설정하는 방법에 대해 설명합니다.

원하는 경우 [포털](howto-peering-service-portal.md)을 사용하여 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 구성 조건을](prerequisites.md) 검토합니다.
* 피어링 서비스를 사용하도록 설정하려는 구독에서 직접 피어링을 선택합니다. 레거시 직접 피어링을 변환하거나 새 직접 피어링을 만듭니다.
    * 레거시 직접 피어링을 변환하려면 [PowerShell을 사용하여 레거시 직접 피어링에서 Azure 리소스로 변환하는](howto-legacy-direct-powershell.md)방법에 따라 다.
    * 새 직접 피어링을 만들려면 [PowerShell을 사용하여 직접 피어링 만들기 또는 수정의](howto-direct-powershell.md)지침을 따릅니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어링에 Peering Service 사용 설정

### <a name="view-direct-peering"></a><a name= get></a>직접 피어링 보기
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>피어링 서비스를 위한 직접 피어링 사용

이전 단계에서 직접 피어링을 받은 후 피어링 서비스에 대해 사용하도록 설정합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어링 연결 수정

연결 설정을 수정해야 하는 경우 [PowerShell을 사용하여 직접 피어링 만들기에서](howto-direct-powershell.md) **직접 피어링** 섹션 수정또는 수정을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-powershell.md)
* [PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

자주 묻는 질문은 [피어링 서비스 FAQ를](service-faqs.md)참조하십시오.