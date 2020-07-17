---
title: 'PowerShell 스크립트: Azure 데이터 공유에 기존 공유 나열 | Microsoft Docs'
description: 이 PowerShell 스크립트는 공유에 대 한 세부 정보를 나열 하 고 표시 합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "70307133"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>PowerShell을 사용 하 여 보낸 데이터 공유의 세부 정보 보기

이 PowerShell 스크립트는 기존 계정에서 데이터 공유를 나열 하 고 특정 공유의 세부 정보를 가져옵니다.


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
| [AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | 계정에 있는 공유의 목록을 가져오고 나열 합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 powershell 샘플](../../samples-powershell.md)에서 찾을 수 있습니다.
