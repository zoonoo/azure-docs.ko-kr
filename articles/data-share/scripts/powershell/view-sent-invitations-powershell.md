---
title: 'PowerShell 스크립트: 소비자에게 전송된 Azure Data Share 초대 나열'
description: 이 PowerShell 스크립트가 소비자에게 전송된 초대를 가져오는 방법을 알아보고 사용할 수 있는 스크립트 예제를 살펴봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0b39c31271a0382cce3388e9c8176ba943bc43e6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703870"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>PowerShell을 사용하여 데이터 공유 초대 가져오기

이 PowerShell 스크립트는 소비자에게 전송된 초대를 가져옵니다.

## <a name="sample-script"></a>샘플 스크립트
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 전송된 데이터 공유 초대를 가져와 나열합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
