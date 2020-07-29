---
title: 'PowerShell 스크립트: 새 Azure 데이터 공유 계정 만들기 | Microsoft Docs'
description: 이 PowerShell 스크립트는 새 데이터 공유 계정을 만듭니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "70307265"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>PowerShell을 사용 하 여 Azure에서 데이터 공유 계정 만들기

이 PowerShell 스크립트는 새 데이터 공유 계정을 만듭니다. 

## <a name="sample-script"></a>샘플 스크립트

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | 데이터 공유 계정을 만듭니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 powershell 샘플](../../samples-powershell.md)에서 찾을 수 있습니다.
