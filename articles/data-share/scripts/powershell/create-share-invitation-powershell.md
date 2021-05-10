---
title: 'PowerShell 스크립트: Azure Data Share 초대 만들기'
description: 이 PowerShell 스크립트는 데이터 공유 초대를 보냅니다.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 5f65d081e724206c1c64ad08189d1b620bbb4f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221335"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell 스크립트를 사용하여 전송된 데이터 공유의 사용 모니터링

이 PowerShell 스크립트는 데이터 공유 초대를 만듭니다.

## <a name="sample-script"></a>샘플 스크립트


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation) | 데이터 공유 초대를 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
