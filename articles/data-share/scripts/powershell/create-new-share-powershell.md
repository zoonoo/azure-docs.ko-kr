---
title: 'PowerShell 스크립트: 새 Azure 데이터 공유 만들기 | 마이크로 소프트 문서'
description: 이 PowerShell 스크립트는 기존 데이터 공유 계정 내에서 새 데이터 공유를 만듭니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307253"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell을 사용하여 Azure에서 데이터 공유 만들기

이 PowerShell 스크립트는 기존 데이터 공유 계정 내에서 새 데이터 공유를 만듭니다.

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
| [뉴 아즈데이터쉐어](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | 데이터 공유를 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 PowerShell 샘플에서](../../samples-powershell.md)찾을 수 있습니다.
