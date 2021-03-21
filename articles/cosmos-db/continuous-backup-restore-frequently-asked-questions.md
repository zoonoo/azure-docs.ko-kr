---
title: Azure Cosmos DB 지정 시간 복원 기능에 대 한 질문과 대답입니다.
description: 이 문서에서는 연속 백업 모드를 사용 하 여 수행 되는 Azure Cosmos DB 지정 시간 복원 기능에 대 한 질문과 대답을 제공 합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393227"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB 지정 시간 복원 기능 (미리 보기)에 대 한 질문과 대답
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대 한 특정 시점 복원 기능 (연속 백업 모드)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 연속 백업 모드를 사용 하 여 수행 되는 Azure Cosmos DB 지정 시간 복원 기능 (미리 보기)에 대 한 질문과 대답을 제공 합니다.

## <a name="how-much-time-does-it-takes-to-restore"></a>복원 하는 데 소요 되는 시간은 얼마 인가요?
데이터 크기에 종속 되는 복원 기간입니다.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>복원 시간을 현지 시간으로 제출할 수 있나요?
데이터베이스 또는 컨테이너와 같은 주요 리소스가 해당 시점에 존재 하는지 여부에 따라 복원이 수행 되지 않을 수 있습니다. 지정 된 시간 동안 시간을 입력 하 고 선택한 데이터베이스 또는 컨테이너를 확인 하 여 확인할 수 있습니다. 복원할 리소스가 없는 경우 복원 프로세스가 작동 하지 않습니다.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>계정이 복원 되는지 어떻게 추적할 수 있나요?
복원 명령을 제출 하 고 동일한 페이지에서 대기 하 고 나면 작업이 완료 된 후 상태 표시줄에 성공적으로 복원 된 계정 메시지가 표시 됩니다. 복원 된 계정을 검색 하 고 [복원 중인 계정의 상태를 추적할](continuous-backup-restore-portal.md#track-restore-status)수도 있습니다. 복원이 진행 중인 동안에는 계정 상태가 *생성* 되 고 복원 작업이 완료 된 후 계정 상태가 *온라인* 으로 변경 됩니다.

마찬가지로 PowerShell 및 CLI의 경우 다음과 같이 명령을 실행 하 여 복원 작업의 진행률을 추적할 수 있습니다 `az cosmosdb show` .

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

ProvisioningState는 계정이 온라인 상태일 때 *성공 했음을* 보여 줍니다.

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

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>계정이 다른 계정에서 복원 되었는지 확인 하려면 어떻게 해야 하나요?
명령을 실행 하 여 `az cosmosdb show` 출력에서 속성 값을 확인할 수 있습니다 `createMode` . 값이 **복원** 으로 설정 되어 있으면입니다. 계정이 다른 계정에서 복원 되었음을 나타냅니다. 속성에는 `restoreParameters` 와 같이 `restoreSource` 원본 계정 ID가 있는 추가 정보가 있습니다. 매개 변수의 마지막 GUID는 `restoreSource` 원본 계정의 instanceId입니다.

예를 들어 다음 출력에서 원본 계정의 인스턴스 ID는 *7b4bb-f6a0-430e-ade1-638d781830cc* 입니다.

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>공유 처리량 데이터베이스 또는 공유 처리량 데이터베이스 내 컨테이너를 복원 하면 어떻게 되나요?
전체 공유 처리량 데이터베이스가 복원 됩니다. 복원에 대 한 공유 처리량 데이터베이스에서 컨테이너의 하위 집합을 선택할 수 없습니다.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>계정 정의에서 InstanceID의 용도는 무엇 인가요?
지정 된 시점에서 Azure Cosmos DB 계정의 `accountName` 속성은 활성 상태인 동안 전역적으로 고유 합니다. 그러나 계정이 삭제 된 후에는 이름이 같은 다른 계정을 만들 수 있으므로 "accountName"이 더 이상 계정 인스턴스를 식별 하기에 충분 하지 않습니다. 

ID 또는은 `instanceId` 계정 인스턴스의 속성 이며 복원에 동일한 이름을 사용 하는 경우 여러 계정 (live 및 deleted)에서 명확 하 게 구분 하는 데 사용 됩니다. 또는 명령을 실행 하 여 인스턴스 ID를 가져올 수 있습니다 `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . Name 특성 값은 "InstanceID"를 나타냅니다.

## <a name="next-steps"></a>다음 단계

* [연속 백업](continuous-backup-restore-introduction.md) 모드는 무엇입니까?
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.
* [연속 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)