---
title: 'PowerShell 스크립트: Azure 데이터 공유에 기존 공유 나열 | 마이크로 소프트 문서'
description: 이 PowerShell 스크립트는 공유에 대한 세부 정보를 나열하고 표시합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307133"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>PowerShell을 사용하여 전송된 데이터 공유의 세부 정보를 볼 수 있습니다.

이 PowerShell 스크립트는 기존 계정의 데이터 공유를 나열하고 특정 공유의 세부 정보를 가져옵니다.


## <a name="sample-script"></a>샘플 스크립트

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [겟 아즈데이터쉐어](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | 계정의 공유 를 가져옵니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 PowerShell 샘플에서](../../samples-powershell.md)찾을 수 있습니다.
