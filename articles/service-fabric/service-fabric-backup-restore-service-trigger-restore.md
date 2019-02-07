---
title: Azure Service Fabric에서 백업 복원 | Microsoft Docs
description: Service Fabric의 주기적 백업 및 복원 기능을 사용하여 애플리케이션 데이터의 백업에서 데이터를 복구합니다.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 1482497f3767e7533d1d56e6eb63e55cdb5c9ebb
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104646"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Azure Service Fabric에서 백업 복원

Azure Service Fabric에서 Reliable Stateful 서비스 및 Reliable Actors는 요청 및 응답 트랜잭션이 완료된 후 변경 가능하고 신뢰할 수 있는 상태를 유지할 수 있습니다. 상태 저장 서비스는 오랜 시간 다운되거나 재해로 인해 정보가 손실될 수 있습니다. 그러한 경우 서비스는 작업을 유지할 수 있는 허용 가능한 최신 백업에서 복원해야 합니다.

예를 들어, 다음 시나리오를 방지하기 위해 해당 데이터를 백업하는 서비스를 구성할 수 있습니다.

- **재해 복구 사례**: Service Fabric 클러스터 전체가 영구적으로 손실되는 경우
- **데이터 손실 사례**: 서비스 파티션의 복제본 대부분이 영구적으로 손실.
- **데이터 손실 사례**: 실수로 인한 삭제 또는 서비스의 손상. 예를 들어 관리자가 실수로 서비스를 삭제합니다.
- **데이터 손상 사례**: 데이터 손상을 초래하는 서비스 내 버그. 예를 들어 신뢰할 수 있는 컬렉션에 잘못된 데이터를 작성하는 서비스 코드가 업그레이드되는 경우 데이터 손상이 발생할 수 있습니다. 그러한 경우 코드와 데이터 모두 이전 상태로 복구해야 할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

- 복구를 트리거하려면 클러스터에 _FAS(Fault Analysis Service)_ 를 사용하도록 설정해야 합니다.
- _BRS(Backup Restore Service)_ 가 백업을 생성합니다.
- 복원은 파티션에서만 트리거할 수 있습니다.

## <a name="triggered-restore"></a>트리거된 복원

복원은 다음 시나리오 중 하나에 트리거될 수 있습니다.

- _재해 복구_를 위한 데이터 복원.
- _데이터 손상/데이터 손실_을 위한 데이터 복원.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>재해 복구 시 데이터 복원.

전체 Service Fabric 클러스터가 손실된 경우 Reliable Stateful 서비스 및 Reliable Actors의 파티션에 대한 데이터를 복원할 수 있습니다. [백업 스토리지 세부 정보가 포함된 GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)를 사용하는 경우 목록에서 원하는 백업을 선택할 수 있습니다. 백업은 애플리케이션, 서비스 또는 파티션에 대해 열거할 수 있습니다.

다음 예제의 경우 손실된 클러스터가 [Reliable Stateful 서비스 및 Reliable Actors에 대해 정기적 백업 사용](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)에서 참조되는 클러스터와 동일하다고 가정합니다. 이 경우 `SampleApp`이 사용하도록 설정된 백업 정책과 함께 배포되고 복구가 Azure Storage에 구성됩니다.

REST API를 사용하는 PowerShell 스크립트를 실행하여 `SampleApp` 애플리케이션 내의 모든 파티션에 대해 생성된 백업 목록을 반환합니다. API에서 사용할 수 있는 백업을 나열하려면 백업 스토리지 정보가 필요합니다.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

위의 실행에 대한 샘플 출력:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

복원을 트리거하려면 백업 중 하나를 선택합니다. 예를 들어 현재 재해 복구를 위한 백업은 다음 백업이 될 수 있습니다.

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

복원 API의 경우 _BackupId_ 및 _BackupLocation_ 세부 정보를 제공해야 합니다.

또한 [파티션 구성표](service-fabric-concepts-partitioning.md#get-started-with-partitioning)에 설명된 대로 다른 클러스터의 대상 파티션을 선택해야 합니다. 대체 클러스터 백업이 손실된 원래 클러스터에서 파티션 구성표에 지정된 파티션으로 복원됩니다.

대체 클러스터의 파티션 ID가 `1c42c47f-439e-4e09-98b9-88b8f60800c6`인 경우, _범위 분할(UniformInt64Partition)_ 에 대한 상위 키 및 하위 키를 비교하여 원래 클러스터 파티션 ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`로 매핑할 수 있습니다.

_이름 지정된 분할_의 경우 이름 값을 비교하여 대체 클러스터의 대상 파티션을 식별합니다.

다음 [복원 API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)를 사용하여 백업 클러스터 파티션에 대해 복원을 요청합니다.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)를 통해 복원의 진행률을 추적할 수 있습니다.

### <a name="data-restore-for-data-corruptiondata-loss"></a>_데이터 손상_/_데이터 손실_ 시 데이터 복원

_데이터 손실_ 또는 _데이터 손상_의 경우 Reliable Stateful 서비스 및 Reliable Actors의 파티션에 대한 백업을 선택한 백업으로 복원할 수 있습니다.

다음 예제는 [Reliable Stateful 서비스 및 Reliable Actors에 대해 정기적 백업 사용](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)의 구성입니다. 이 예제에서는 백업 정책이 파티션에 사용하도록 설정되어 있으며, 서비스는 Azure Storage에서 원하는 빈도로 백업을 수행합니다.

[GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups)의 출력에서 백업을 선택합니다. 이 시나리오에서는 이전과 동일한 클러스터에서 백업이 생성됩니다.

복원을 트리거하려면 목록에서 백업을 선택합니다. 현재 _데이터 손실_/_데이터 손상_의 경우 다음과 같은 백업을 선택합니다.

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

복원 API의 경우 _BackupId_ 및 _BackupLocation_ 세부 정보를 제공합니다. 클러스터에서 백업이 설정되었기 때문에 Service Fabric _BRS(Backup Restore Service)_ 는 연결된 백업 정책에서 올바른 스토리지 위치를 식별합니다.

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)를 통해 복원의 진행률을 추적할 수 있습니다.

## <a name="track-restore-progress"></a>복원 진행률 추적

Reliable Stateful 서비스 또는 Reliable Actor의 파티션은 한 번에 하나의 복원 요청만 수락합니다. 파티션은 현재 복원 요청이 완료된 후에만 다른 요청을 수락합니다. 여러 복원 요청은 다른 파티션에서 동시에 트리거될 수 있습니다.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

복원 요청은 다음 순서대로 진행됩니다.

1. **수락됨**: _수락됨_ 복원 상태는 올바른 요청 매개 변수를 사용하여 요청된 파티션이 트리거됨을 나타냅니다.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **진행 중**: _진행 중_ 복원 상태는 요청에서 언급된 백업을 사용하여 파티션에서 복원이 발생 중임을 나타냅니다. 파티션은 _데이터 손실_ 상태를 보고합니다.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **성공**, **실패** 또는 **시간 초과**: 요청된 복원은 다음 상태로 완료될 수 있습니다. 각 상태에는 다음과 같은 중요도 및 응답 세부 정보가 있습니다.
    - **성공**: _성공_ 복원 상태는 회복된 파티션 상태를 표시합니다. 파티션은 시간(UTC)과 함께 _RestoredEpoch_ 및 _RestoredLSN_을 보고합니다.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **실패**: _실패_ 복원 상태는 복원 요청의 실패를 나타냅니다. 오류의 원인이 보고됩니다.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **시간 초과**: _시간 초과_ 복원 상태는 요청 시간이 초과되었음을 나타냅니다. [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)이 더 큰 새 복원 요청을 만듭니다. 시간 제한은 기본적으로 10분입니다. 복원을 다시 요청하기 전에 파티션이 데이터 손실 상태에서 벗어났는지 확인해야 합니다.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>자동 복원

_자동 복원_을 위해 Service Fabric 클러스터의 Reliable Stateful 서비스 및 Reliable Actors 파티션을 구성할 수 있습니다. 백업 정책에서 `AutoRestore`를 _true_로 설정합니다. _자동 복원_을 사용하도록 설정하면 데이터 손실이 보고될 때 최신 파티션 백업에서 데이터가 자동으로 복원됩니다. 자세한 내용은 다음을 참조하세요.

- [백업 정책에서 자동 복원을 사용하도록 설정](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>다음 단계
- [정기 백업 구성 이해](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [백업 복원 REST API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
