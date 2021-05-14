---
title: Azure PowerShell을 사용하여 Azure Cosmos DB에서 지속적인 백업 및 특정 시점 복원을 구성합니다.
description: 지속적인 백업으로 계정을 프로비전하고 Azure PowerShell을 사용하여 데이터를 복원하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381837"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>지속적인 백업 및 특정 시점 복원(미리 보기) 구성 및 관리 - Azure PowerShell 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 지속적인 백업을 사용하여 계정을 프로비전하고 Azure PowerShell을 사용하여 데이터를 복원하는 방법을 설명합니다.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Azure PowerShell 설치

1. Azure PowerShell에서 다음 명령을 실행하여 특정 시점 복원과 관련된 명령이 포함된 `Az.CosmosDB` 미리 보기 모듈을 설치합니다.

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. 모듈을 설치한 후 다음을 사용하여 Azure에 로그인합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

1. 다음 명령을 사용하여 특정 구독을 선택합니다.

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>지속적인 백업으로 SQL API 계정 프로비저닝

지속적인 백업으로 계정을 프로비전하려면 일반 프로비전 명령과 함께 `-BackupPolicyType Continuous` 인수를 추가합니다.

다음 cmdlet은 미국 서부 지역에서 *myrg* 리소스 그룹 아래에 지속적인 백업 정책을 사용하여 생성된 단일 지역 쓰기 계정 `pitracct2`의 예입니다 *.*

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>지속적인 백업으로 MongoDB API 계정 프로비저닝

다음 cmdlet은 미국 서부 지역에서 *myrg* 리소스 그룹 아래에 지속적인 백업 정책을 사용하여 생성된 지속적인 백업 계정 *pitracct2* 의 예입니다 *.*

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>복원 작업 트리거

다음 cmdlet은 대상 계정, 원본 계정, 위치, 리소스 그룹 및 타임스탬프를 사용하여 복원 명령으로 복원 작업을 트리거하는 예입니다.

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**예제 1:** 전체 계정 복원:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**예제 2:** 특정 컬렉션 및 데이터베이스 복원. 이 예제는 myDB1에서 컬렉션 myCol1, myCol2를 복원하고, myDB2에서 그 아래에 있는 모든 컨테이너를 포함한 전체 데이터베이스를 복원합니다.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API에 대해 복원 가능한 리소스 열거

열거형 cmdlet은 다양한 타임스탬프에서 복원이 가능한 리소스를 검색하는 데 도움이 됩니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다.

**현재 구독에서 복원할 수 있는 모든 계정 나열**

`Get-AzCosmosDBRestorableDatabaseAccount`PowerShell 명령을 실행하여 현재 구독에서 복원할 수 있는 모든 계정을 나열합니다.

응답에는 복원할 수 있는 모든 데이터베이스 계정(라이브 및 삭제된 계정 모두)과 복원할 수 있는 지역이 포함됩니다.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

계정에 대한 `CreationTime` 또는 `DeletionTime`와 마찬가지로 지역에도 `CreationTime` 또는 `DeletionTime`가 있습니다. 이러한 시간을 사용하여 올바른 지역 및 해당 지역으로 복원할 유효한 시간 범위를 선택할 수 있습니다.

**라이브 데이터베이스 계정에서 모든 버전의 SQL 데이터베이스를 나열**

모든 버전의 데이터베이스를 나열하면 데이터베이스의 실제 존재 시간을 알 수 없는 시나리오에서 올바른 데이터베이스를 선택할 수 있습니다.

다음 PowerShell 명령을 실행하여 모든 버전의 데이터베이스를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `DatabaseAccountInstanceId` 및 `LocationName` 매개 변수는 `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet의 응답에서 `name` 및 `location` 속성으로부터 가져옵니다. `DatabaseAccountInstanceId` 특성은 복원되는 원본 데이터베이스 계정의 `instanceId` 속성을 참조합니다.


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**라이브 데이터베이스 계정의 데이터베이스에서 모든 SQL 컨테이너 버전을 나열**

다음 명령을 사용하여 모든 버전의 SQL 컨테이너를 나열합니다. 이 명령은 라이브 계정에서만 작동합니다. `DatabaseRid` 매개 변수는 복원하려는 데이터베이스의 `ResourceId`입니다. `Get-AzCosmosdbSqlRestorableDatabase` cmdlet의 응답에 포함된 `ownerResourceid` 특성의 값입니다. 이 응답에는 이 데이터베이스 내의 모든 컨테이너에 대해 수행된 작업의 목록도 포함되어 있습니다.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너 찾기**

다음 명령을 사용하여 특정 타임스탬프에서 복원할 수 있는 데이터베이스 또는 컨테이너의 목록을 가져옵니다. 이 명령은 라이브 계정에서만 작동합니다.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>MongoDB에 대해 복원 가능한 리소스 열거

아래에 설명된 열거 명령은 다양한 타임스탬프에서 복원에 사용할 수 있는 리소스를 검색하는 데 유용합니다. 또한 복원 가능한 계정, 데이터베이스 및 컨테이너 리소스에 대한 주요 이벤트의 피드를 제공합니다. 이들 명령은 라이브 계정에 대해서만 작동하며 SQL API 명령과 비슷하지만 명령 이름에 `sql` 대신 `MongoDB`가 들어갑니다.

**라이브 데이터베이스 계정에서 모든 버전의 MongoDB 데이터베이스를 나열**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**라이브 데이터베이스 계정의 데이터베이스에서 모든 버전의 MongoDB 컬렉션을 나열**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**특정 타임스탬프 및 지역에서 복원에 사용할 수 있는 MongoDB 데이터베이스 계정의 모든 리소스를 나열**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>다음 단계

* [Azure CLI](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md) 또는 [Azure Portal](continuous-backup-restore-portal.md)을 사용하여 지속적인 백업 구성 및 관리
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md).
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한 관리](continuous-backup-restore-permissions.md).
