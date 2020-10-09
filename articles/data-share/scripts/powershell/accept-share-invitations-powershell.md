---
title: 'PowerShell 스크립트: Azure 데이터 공유의 초대 수락 | Microsoft Docs'
description: 이 PowerShell 스크립트는 기존 데이터 공유의 초대를 수락 합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "70307343"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>PowerShell을 사용 하 여 데이터 공유 초대 수락

이 PowerShell 스크립트는 소비자에 게 보낸 초대를 수락 합니다.

## <a name="sample-script"></a>샘플 스크립트
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 전송 데이터 공유 초대를 가져오고 나열 합니다. |
| [AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | 데이터 공유 구독을 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 powershell 샘플](../../samples-powershell.md)에서 찾을 수 있습니다.

