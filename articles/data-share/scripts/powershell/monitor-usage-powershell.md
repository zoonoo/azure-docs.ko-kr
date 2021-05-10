---
title: 'PowerShell 스크립트: Azure Data Share의 사용 현황 모니터링'
description: 이 PowerShell 스크립트는 전송된 데이터 공유의 사용 현황 메트릭을 검색합니다.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: e9ff7a29cba9b8e9ca058bfe742f484c5b495cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221318"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell을 사용하여 전송된 데이터 공유의 사용 현황 모니터링

이 PowerShell 스크립트는 전송된 데이터 공유의 동기화를 나열하고 특정 동기화의 세부 정보를 가져와 데이터 사용 현황을 모니터링합니다.

## <a name="sample-script"></a>샘플 스크립트


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization) | 공유에 대한 동기화를 나열합니다. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail) | 공유 동기화의 동기화 세부 정보를 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
