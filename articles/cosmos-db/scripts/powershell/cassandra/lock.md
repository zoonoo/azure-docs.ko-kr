---
title: Azure Cosmos Cassandra API 키스페이스 및 테이블에 대한 리소스 잠금을 만드는 PowerShell 스크립트
description: Azure Cosmos Cassandra API 키스페이스 및 테이블에 대한 리소스 잠금 만들기
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: f8e1e2172137c1c6a569fb169c59191ab11cedfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99821791"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Cosmos Cassandra API 키스페이스 및 테이블에 대한 리소스 잠금 만들기
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell Az 5.4.0 이상이 필요합니다. `Get-Module -ListAvailable Az`를 실행하여 설치된 버전을 확인합니다.
설치해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure에 로그인합니다.

> [!IMPORTANT]
> `disableKeyBasedMetadataWriteAccess` 속성을 사용하도록 설정된 상태에서 먼저 Cosmos DB 계정이 잠겨 있지 않는 한 Cassandra SDK, CQL Shell 또는 Azure Portal을 사용하여 연결한 사용자가 변경한 내용에 대해서는 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하도록 설정하는 방법에 대한 자세한 내용은[Sdk변경 방지](../../../role-based-access-control.md#prevent-sdk-changes)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure 리소스**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | 리소스 잠금을 만듭니다. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | 리소스 잠금을 가져오거나 리소스 잠금을 나열합니다. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | 리소스 잠금을 제거합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.