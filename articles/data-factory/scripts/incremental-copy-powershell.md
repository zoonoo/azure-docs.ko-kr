---
title: 'PowerShell 스크립트: Azure Data Factory를 사용하여 데이터 증분 로드 | Microsoft Docs'
description: 이 PowerShell 스크립트에서는 Azure Data Factory를 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터를 증분 방식으로 복사하는 방법을 보여줍니다.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440593"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell 스크립트: Azure Data Factory를 사용하여 증분 방식으로 데이터 로드
이 샘플 PowerShell 스크립트는 원본에서 싱크로 데이터를 초기 전체 복사한 후에 원본 데이터 저장소에서 싱크 데이터 저장소로 새롭거나 업데이트된 레코드만을 로드합니다.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

이 샘플을 실행하기 위한 필수 구성 요소는 [자습서: 증분 복사](../tutorial-incremental-copy-powershell.md#prerequisites)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
리소스 그룹에서 Data Factory를 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 데이터 팩터리에서 데이터 세트를 만듭니다. 데이터 세트는 파이프라인의 작업에 대한 입/출력을 나타냅니다. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 작업이 있습니다. 이 파이프라인에서 복사 작업은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Data Factory PowerShell 스크립트 샘플은 [Azure Data Factory PowerShell 스크립트](../samples-powershell.md)에 있습니다.
