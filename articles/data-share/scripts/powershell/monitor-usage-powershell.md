---
title: 'PowerShell 스크립트: Azure 데이터 공유의 사용량 모니터링 | 마이크로 소프트 문서'
description: 이 PowerShell 스크립트는 보낸 데이터 공유의 사용 메트릭을 검색합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307198"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell을 사용하여 전송된 데이터 공유의 사용량모니터링

이 PowerShell 스크립트는 전송된 데이터 공유의 동기화를 나열하고 특정 동기화의 세부 정보를 얻어서 데이터 사용량을 모니터링합니다.

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
| [받기-아즈데이터공유동기화](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | 공유에 동기화를 나열합니다. |
| [가져오기-아즈데이터공유동기화세부사항](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | 공유 동기화의 동기화 세부 정보를 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 PowerShell 샘플에서](../../samples-powershell.md)찾을 수 있습니다.
