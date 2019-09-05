---
title: 'PowerShell 스크립트: Azure 데이터 공유에 blob 데이터 집합 추가 | Microsoft Docs'
description: 이 PowerShell 스크립트는 기존 공유에 blob 데이터 집합을 추가 합니다.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 95a10b112c9f6448c437f20ee95f808632a31d2d
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307311"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell을 사용 하 여 Azure에서 데이터 공유 만들기

이 PowerShell 스크립트는 기존 데이터 공유에 blob 데이터 집합을 추가 합니다.

## <a name="sample-script"></a>샘플 스크립트

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 참고 |
|---|---|
| [AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset?view=azps-2.6.0) | 데이터 집합을 데이터 공유에 추가 합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure 데이터 공유 PowerShell 스크립트 샘플은 [Azure 데이터 공유 powershell 샘플](../../samples-powershell.md)에서 찾을 수 있습니다.
