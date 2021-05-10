---
title: 'PowerShell 스크립트: 새 Azure Data Share 만들기'
description: 이 PowerShell 스크립트는 기존 Data Share 계정 내에 새 데이터 공유를 만듭니다.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221352"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell을 사용하여 Azure에서 데이터 공유 만들기

이 PowerShell 스크립트는 기존 Data Share 계정 내에 새 데이터 공유를 만듭니다.

## <a name="sample-script"></a>샘플 스크립트

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | 데이터 공유를 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Data Share PowerShell 스크립트 샘플은 [Azure Data Share PowerShell 샘플](../../samples-powershell.md)에 있습니다.
