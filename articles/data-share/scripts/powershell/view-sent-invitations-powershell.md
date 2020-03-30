---
title: 'PowerShell 스크립트: 소비자에게 보낸 Azure 데이터 공유 초대 목록 | 마이크로 소프트 문서'
description: 이 PowerShell 스크립트는 기존 데이터 공유의 초대를 수락합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307146"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>PowerShell을 사용하여 데이터 공유 초대를 받으세요.

이 PowerShell 스크립트는 소비자에게 초대장을 받습니다.

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
| [겟-아즈데이터쉐어초대장](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | 전송된 데이터 공유 초대장을 받고 나열합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 PowerShell 샘플에서](../../samples-powershell.md)찾을 수 있습니다.
