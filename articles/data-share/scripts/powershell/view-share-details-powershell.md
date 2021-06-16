---
title: 'PowerShell 스크립트: Azure Data Share의 기존 공유를 나열합니다.'
description: 이 PowerShell 스크립트는 공유에 대한 세부 정보를 나열하고 표시합니다.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2692cbd836b351e5c41dfe40168b365885997ac0
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703897"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>PowerShell을 사용하여 전송된 데이터 공유의 세부 정보 보기

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
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | 계정에 있는 공유를 가져오고 공유 목록을 나열합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
