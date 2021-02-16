---
title: Azure Cosmos DB 계정에 대한 키 및 연결 문자열 작업을 가져오는 PowerShell 스크립트
description: Azure PowerShell 스크립트 샘플 - Azure Cosmos DB 계정에 대한 계정 키 및 연결 문자열 작업
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: eb84684201ddf79f2a73fd1bcba1a0f69899bea8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "99821678"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>PowerShell을 사용하여 Azure Cosmos DB 계정에 대한 연결 문자열 및 계정 키 작업
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell Az 5.4.0 이상이 필요합니다. `Get-Module -ListAvailable Az`를 실행하여 설치된 버전을 확인합니다.
설치해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure에 로그인합니다.

## <a name="sample-script"></a>샘플 스크립트

> [!NOTE]
> 이 샘플에서는 SQL API 계정을 사용하는 방법을 보여줍니다. 이 샘플을 다른 API에 사용하려면 관련 속성을 복사하고 API별 스크립트에 적용합니다.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Cosmos DB 계정에 대한 연결 문자열 또는 키(읽기-쓰기 또는 읽기 전용)를 가져옵니다. |
| [New-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | Cosmos DB 계정에 대해 지정된 키를 다시 생성합니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.