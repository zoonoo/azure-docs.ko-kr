---
title: PowerShell을 사용하여 데이터를 점진적으로 로드
description: 이 PowerShell 스크립트는 Azure 데이터 팩터리를 사용하여 Azure SQL 데이터베이스에서 Azure Blob 저장소로 데이터를 증분 방식으로 복사하는 방법을 보여 주며 있습니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 1919f89d2e39981effe14c1203446f8f9d930f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462492"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell 스크립트: Azure Data Factory를 사용하여 증분 방식으로 데이터 로드

이 샘플 PowerShell 스크립트는 원본에서 싱크로 데이터를 초기 전체 복사한 후에 원본 데이터 저장소에서 싱크 데이터 저장소로 새롭거나 업데이트된 레코드만을 로드합니다.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

이 샘플을 실행하기 위한 필수 구성 요소는 [자습서: 증분 복사](../tutorial-incremental-copy-powershell.md#prerequisites)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

> [!IMPORTANT]
> 이 스크립트는 하드 드라이브의 c:\ 폴더에 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 정의하는 JSON 파일을 만듭니다.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

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
| [세트 아즈데이팩토리V2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 데이터 팩터리를 만듭니다. |
| [세트 아즈데이터팩토리V2링크드서비스](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 데이터 팩터리에서 연결된 서비스를 만듭니다. 연결된 서비스는 데이터 저장소 또는 컴퓨팅을 데이터 팩터리에 연결합니다. |
| [세트 아즈데이팩토리V2데이터세트](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 데이터 팩터리에서 데이터 세트를 만듭니다. 데이터 세트는 파이프라인의 작업에 대한 입/출력을 나타냅니다. | 
| [세트 아즈데이데이팩토리V2파이프라인](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 데이터 팩터리에서 파이프라인을 만듭니다. 파이프라인에는 특정 작업을 수행하는 하나 이상의 활동이 포함됩니다. 이 파이프라인에서 복사 작업은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. |
| [인보크-아즈데이데이팩토리V2파이프라인](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 파이프라인에 대한 실행을 만듭니다. 즉, 파이프라인을 실행합니다. |
| [겟-아즈데이데이팩토리V2액티브런](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 파이프라인의 작업 실행 방법에 대한 세부 정보를 가져옵니다. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/)를 참조하세요.

추가 Azure Data Factory PowerShell 스크립트 샘플은 [Azure Data Factory PowerShell 스크립트](../samples-powershell.md)에 있습니다.
