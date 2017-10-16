---
title: "PowerShell 스크립트 - Data Factory를 사용하여 클라우드의 데이터 변환 | Microsoft Docs"
description: "이 PowerShell 스크립트는 Azure HDInsight Spark 클러스터에서 Spark 프로그램을 실행하여 클라우드의 데이터를 변환합니다."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 42135f0a0101ba82bca0d662ae4b970f799f70d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell 스크립트 - Azure Data Factory를 사용하여 클라우드의 데이터 변환

이 샘플 PowerShell 스크립트는 Azure HDInsight Spark 클러스터에서 Spark 프로그램을 실행하여 클라우드의 데이터를 변환하는 파이프라인을 만듭니다. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>샘플 스크립트
> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 집합 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>배포 정리

샘플 스크립트를 실행한 후에는 다음 명령을 사용하여 리소스 그룹 및 해당 그룹에 연결된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```
리소스 그룹에서 Data Factory를 제거하려면 다음 명령을 실행합니다. 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<data factory name>" -ResourceGroupName "<resource group name>"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.

| 명령 | 참고 사항 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 계산을 데이터 팩터리에 연결합니다. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 작업이 있습니다. 이 파이프라인에서 Spark 작업은 Azure HDInsight Spark 클러스터에서 프로그램을 실행하여 데이터를 변환합니다. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Cosmos DB PowerShell 스크립트 샘플은 [Azure Cosmos DB PowerShell 스크립트](../samples-powershell.md)에 있습니다.