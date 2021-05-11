---
title: PowerShell을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정
titleSuffix: Azure
description: PowerShell을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079050"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>PowerShell을 통해 직접 피어링에 Azure Peering Service를 사용하도록 설정

이 문서에서는 PowerShell cmdlet 및 Azure Resource Manager 배포 모델을 통해 직접 피어링에 Azure [Peering Service](overview-peering-service.md)를 사용하도록 설정하는 방법을 설명합니다.

원할 경우 Azure [Portal](howto-peering-service-portal.md)을 통해 이 가이드를 완료할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
* 구성을 시작하기 전에 [필수 조건](prerequisites.md)을 검토합니다.
* 구독에서 Peering Service를 사용하도록 설정할 직접 피어링을 선택합니다. 직접 피어링이 없으면 레거시 직접 피어링을 변환하거나 새 직접 피어링을 만듭니다.
    * 레거시 직접 피어링을 변환하려면 [PowerShell을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-powershell.md)의 지침을 수행합니다.
    * 새 직접 피어링을 만들려면 [PowerShell을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-powershell.md)의 지침을 수행합니다.

### <a name="work-with-azure-powershell"></a>Azure PowerShell을 사용하여 작업
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>직접 피어링에 Peering Service 사용 설정

### <a name="view-direct-peering"></a><a name= get></a>직접 피어링 보기
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>직접 피어링에 Peering Service를 사용하도록 설정

이전 단계에서 직접 피어링을 가져온 후에는 직접 피어링에 Peering Service를 사용하도록 설정합니다.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>직접 피어링 연결 수정

연결 설정을 수정해야 하는 경우 [PowerShell을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-powershell.md)에서 “직접 피어링 수정” 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-powershell.md)
* [PowerShell을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>추가 리소스
다음 명령을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
Get-Help Get-AzPeering -detailed
```

질문과 대답은 [Peering Service FAQ](service-faqs.md)를 참조하세요.
