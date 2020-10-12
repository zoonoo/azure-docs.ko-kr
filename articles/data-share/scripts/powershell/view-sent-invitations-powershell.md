---
title: 'PowerShell 스크립트: 소비자에 게 보낸 Azure 데이터 공유 초대 나열 | Microsoft Docs'
description: 이 PowerShell 스크립트를 통해 소비자에 게 보낸 초대를 가져오고 사용할 수 있는 스크립트의 예를 확인 하는 방법을 알아봅니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985629"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>PowerShell을 사용 하 여 데이터 공유 초대 가져오기

이 PowerShell 스크립트는 소비자에 게 보낸 초대를 가져옵니다.

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
| [AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 전송 데이터 공유 초대를 가져오고 나열 합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 powershell 샘플](../../samples-powershell.md)에서 찾을 수 있습니다.
