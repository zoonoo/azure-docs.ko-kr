---
title: Azure Cosmos DB 특정 시점 복원 기능에 대한 질문과 대답
description: 이 문서에서는 지속적인 백업 모드를 사용하여 수행되는 Azure Cosmos DB 특정 시점 복원 기능에 대한 질문과 대답을 제공합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393227"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB 특정 시점 복원 기능(미리 보기)에 대한 질문과 대답
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 지속적인 백업 모드를 사용하여 수행되는 Azure Cosmos DB 특정 시점 복원 기능(미리 보기)에 대한 질문과 대답을 제공합니다.

## <a name="how-much-time-does-it-takes-to-restore"></a>복원하는 데 소요되는 시간은 얼마인가요?
복원 시간은 데이터 크기에 따라 다릅니다.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>복원 시간을 현지 시간으로 제출할 수 있나요?
데이터베이스 또는 컨테이너와 같은 주요 리소스가 해당 시점에 존재하는지 여부에 따라 복원이 수행되지 않을 수 있습니다. 시간을 입력하고 일정 시간 동안 선택한 데이터베이스 또는 컨테이너를 모니터링하여 확인할 수 있습니다. 복원할 리소스가 없는 경우 복원 프로세스가 작동하지 않습니다.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>계정이 복원되는지 어떻게 추적할 수 있나요?
복원 명령을 제출하고 동일한 페이지에서 대기하면 작업이 완료된 후 상태 표시줄에 계정이 성공적으로 복원되었다는 메시지가 표시됩니다. 복원된 계정을 검색하고 [복원 중인 계정의 상태를 추적](continuous-backup-restore-portal.md#track-restore-status)할 수도 있습니다. 복원이 진행 중인 동안에는 계정 상태가 '생성 중'이고, 복원 작업이 완료된 후에는 계정 상태가 '온라인'으로 변경됩니다.

마찬가지로 PowerShell 및 CLI의 경우 다음과 같이 `az cosmosdb show` 명령을 실행하여 복원 작업의 진행률을 추적할 수 있습니다.

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

계정이 온라인 상태이면 provisioningState에 '성공함'이 표시됩니다.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>계정이 다른 계정에서 복원되었는지 확인하려면 어떻게 해야 하나요?
`az cosmosdb show` 명령을 실행하여 출력에서 `createMode` 속성 값을 확인할 수 있습니다. 이 값이 **복원** 으로 설정되어 있는 경우 계정이 다른 계정에서 복원된 것을 나타냅니다. `restoreParameters` 속성에는 `restoreSource`(원본 계정 ID)와 같은 추가 정보가 있습니다. `restoreSource` 매개 변수의 마지막 GUID는 원본 계정의 instanceId입니다.

예를 들어 다음 출력에서 원본 계정의 ID는 *7b4bb-f6a0-430e-ade1-638d781830cc* 입니다.

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>공유 처리량 데이터베이스 또는 공유 처리량 데이터베이스 내의 컨테이너를 복원하면 어떻게 되나요?
전체 공유 처리량 데이터베이스가 복원됩니다. 복원할 공유 처리량 데이터베이스에서 컨테이너 하위 집합을 선택할 수 없습니다.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>계정 정의에서 InstanceID의 용도는 무엇인가요?
Azure Cosmos DB 계정이 활성 상태인 동안 특정 시점에서 계정의 `accountName` 속성은 전역적으로 고유합니다. 그러나 계정이 삭제된 후에는 동일한 이름으로 다른 계정을 만들 수 있으므로 'accountName'이 더 이상 계정 인스턴스를 식별하기에 충분하지 않습니다. 

ID 또는 `instanceId`는 계정 인스턴스의 속성이며 복원에서 여러 계정(라이브 계정 및 삭제된 계정)이 동일한 이름을 사용하는 경우 모호성을 제거하는 데 사용됩니다. `Get-AzCosmosDBRestorableDatabaseAccount` 또는 `az cosmosdb restorable-database-account` 명령을 실행하여 인스턴스 ID를 가져올 수 있습니다. 이름 특성 값이 'InstanceID'를 나타냅니다.

## <a name="next-steps"></a>다음 단계

* [지속적인 백업](continuous-backup-restore-introduction.md) 모드란 무엇인가요?
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 지속적인 백업을 구성하고 관리합니다.
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md)합니다.
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)