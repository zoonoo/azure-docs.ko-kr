---
title: 'PowerShell 스크립트: Azure Data Share의 초대 수락'
description: 이 PowerShell 스크립트는 기존 데이터 공유의 초대를 수락합니다.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ce2a315512af65147fa2354c5895bcff94710cf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697160"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>PowerShell을 사용하여 데이터 공유 초대 수락

이 PowerShell 스크립트는 소비자에게 보낸 초대를 수락합니다.

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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 전송된 데이터 공유 초대를 가져와 나열합니다. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | 데이터 공유 구독을 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
