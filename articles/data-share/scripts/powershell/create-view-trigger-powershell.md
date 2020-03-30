---
title: 'PowerShell 스크립트: Azure 데이터 공유 스냅숏 트리거 생성 및 보기 | 마이크로 소프트 문서'
description: 이 PowerShell 스크립트는 공유 스냅샷 트리거를 만들고 가져옵니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4f3148a4d5bd0d39ccfcf7e92e80300a7e19effa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307227"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>PowerShell을 사용하여 전송된 데이터 공유의 사용량모니터링

이 PowerShell 스크립트는 공유 스냅샷 트리거를 만들고 가져옵니다.

## <a name="sample-script"></a>샘플 스크립트

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [뉴 아즈데이터쉐어트리거](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | 공유 스냅숏 트리거를 만듭니다. |
| [겟 아즈데이터쉐어트리거](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | 공유 동기화의 동기화 설정을 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 PowerShell 샘플에서](../../samples-powershell.md)찾을 수 있습니다.
