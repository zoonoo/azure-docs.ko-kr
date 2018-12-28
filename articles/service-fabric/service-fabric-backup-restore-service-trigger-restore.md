---
title: Azure Service Fabric에서 백업 복원 | Microsoft Docs
description: Service Fabric의 주기적 백업 및 복원 기능을 사용하여 애플리케이션 데이터 백업에서 데이터를 복구합니다.
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
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730723"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Azure Service Fabric에서 백업 복원


Service Fabric의 Reliable Stateful 서비스 및 Reliable Actors는 요청 및 응답 또는 완전한 트랜잭션을 넘어서서 변경 가능하고 신뢰할 수 있는 상태를 유지할 수 있습니다. 상태 저장 서비스가 오랜 시간 동안 중단되거나 재해로 인해 정보가 손실되는 경우 백업된 후에 서비스를 계속 제공할 수 있도록 허용 가능한 상태의 최신 백업으로 복원해야 할 수 있습니다.

예를 들어 서비스에서 다음과 같은 시나리오로부터 보호하기 위해 해당 데이터를 백업하려고 할 수 있습니다.

- Service Fabric 클러스터 전체가 영구적으로 손실되는 경우 **(재해 복구 사례 - DR)**
- 서비스 파티션의 복제본 대부분이 영구적으로 손실. **(데이터 손실인 경우)**
- 상태가 우발적으로 삭제되거나 손상된 관리 오류. 예를 들어 충분한 권한이 있는 관리자가 실수로 서비스를 삭제합니다.**(데이터 손실인 경우)**
- 데이터 손상을 초래하는 서비스 내 버그. 예를 들어 신뢰할 수 있는 컬렉션에 잘못된 데이터 작성을 시작하는 서비스 코드가 업그레이드되는 경우 데이터 손상이 발생할 수 있습니다. 이런 경우 코드와 데이터 모두 이전 상태로 되돌아가야 할 수도 있습니다. **(데이터 손상인 경우)**


## <a name="prerequisites"></a>필수 조건
* 트리거하려면 클러스터에 _FAS(Fault Analysis Service)_ 복구를 사용하도록 설정
* 백업 복원이 _BRS(Backup Restore Service)_ 를 통해 수행되어야 함
* 복원은 파티션에서만 트리거할 수 있습니다.

## <a name="triggering-restore"></a>복원 트리거

복원은 다음 시나리오 중 하나에 사용할 수 있습니다. 
* _DR(재해 복구)_ 시 데이터 복원
* _데이터 손상/데이터 손실_ 발생 시 데이터 복원



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>_DR(재해 복구)_ 시 데이터 복원
Service Fabric 클러스터 전체가 손실된 경우 Reliable Stateful 서비스 및 Reliable Actors의 파티션에 대한 데이터를 대체 클러스터에 복원할 수 있습니다. [백업 스토리지 세부 정보가 포함된 GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation)의 열거형에서 원하는 백업을 선택할 수 있습니다. 백업은 애플리케이션, 서비스 또는 파티션에 대해 열거할 수 있습니다.

손실된 클러스터가 [Reliable Stateful 서비스 및 Reliable Actors에 대해 정기적 백업 사용](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)에서 언급한 클러스터이고, `SampleApp`이 배포되었고, 파티션에서 백업 정책이 사용되고 Azure Storage에서 백업이 발생했다고 가정해 보겠습니다. 


REST API를 호출하는 다음 PowerShell 스크립트를 실행하여 손실된 Service Fabric 클러스터의 `SampleApp` 애플리케이션 내부에 있는 모든 파티션에 대해 생성된 백업을 열거합니다. 열거형 API를 사용하려면 스토리지 정보가 필요하며, 사용 가능한 백업을 열거하기 위해 애플리케이션 백업이 저장됩니다. 

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



복원을 트리거하려면 원하는 백업을 선택해야 합니다. 현재 DR(재해 복구)에 원하는 백업이 다음 백업이라고 가정해 봅시다.

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

복원 API의 경우 __BackupId__ 및 __BackupLocation__ 세부 정보를 제공해야 합니다. [파티션 구성표](service-fabric-concepts-partitioning.md#get-started-with-partitioning)에 따라 대체 클러스터의 파티션을 선택해야 합니다. 손실된 원래 클러스터의 파티션 구성표에 따라 대체 클러스터의 백업을 복원할 대상 파티션을 선택하는 것은 사용자 책임입니다.

대체 클러스터의 파티션 ID가 `1c42c47f-439e-4e09-98b9-88b8f60800c6`이고, _범위 분할(UniformInt64Partition)_ 에 대한 상위 키 및 하위 키를 비교하여 원래 클러스터 파티션 ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`로 매핑된다고 가정하겠습니다.

_이름 지정된 분할_의 경우 이름 값을 비교하여 대체 클러스터의 대상 파티션을 식별합니다.

[복원 API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)를 따라 백업 클러스터의 파티션에 대해 복원이 요청됩니다.

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
복원 진행률은 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)일 수 있습니다.

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>_데이터 손상/데이터 손실_ 발생 시 데이터 복원

_데이터 손실_ 또는 _데이터 손상_인 경우 Reliable Stateful 서비스 및 Reliable Actors의 파티션에 대한 데이터를 선택한 백업으로 복원할 수 있습니다. 다음 사례는 [Reliable Stateful 서비스 및 Reliable Actors에 대해 정기적 백업 사용](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)에 언급된 샘플의 후속편으로, 파티션에서 백업 정책이 사용되고 Azure Storage에서 원하는 빈도로 백업이 발생합니다. 

[GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) 출력에서 원하는 백업을 선택합니다. 이 시나리오에서는 이전에 동일한 클러스터에서 백업이 생성됩니다.
복원을 트리거하려면 목록에서 원하는 백업을 선택해야 합니다. 현재 _데이터 손실_ / _데이터 손상_에 대해 원하는 백업이 다음과 같다고 가정해 봅시다.

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

복원 API의 경우 __BackupId__ 및 __BackupLocation__ 세부 정보를 제공해야 합니다. 클러스터에서 백업이 설정되었기 때문에 Service Fabric _BRS(Backup Restore Service )_ 는 연결된 백업 정책에서 올바른 스토리지 위치를 식별합니다.

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

복원 진행률은 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)일 수 있습니다.


## <a name="tracking-restore-progress"></a>복원 진행률 추적

Reliable Stateful 서비스 또는 Reliable Actor의 파티션은 한 번에 하나의 복원 요청만 수락합니다. 현재 복원 요청이 완료되어야만 또 다른 요청을 수락할 수 있습니다. 여러 복원 요청을 다른 파티션에서 동시에 트리거할 수 있습니다.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

복원 요청은 다음 순서대로 진행됩니다.

1. __수락됨__ - 복원 상태가 _수락됨_이면 올바른 요청 매개 변수를 사용하여 요청이 트리거된 것입니다.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __진행 중__ - 복원 상태가 _진행 중_이면 파티션이 요청에 언급된 백업을 사용하여 곧 복원됩니다. 파티션에서 _데이터 손실_ 상태를 보고할 것입니다.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __성공__/ __실패__/ __시간 제한__ - 요청된 복원이 다음 상태로 완료될 수 있습니다. 각 상태의 중요도 및 응답 세부 정보는 다음과 같습니다.
       
    * __성공__ - 복원 상태가 _성공_이면 파티션 상태가 다시 설정된 것입니다. 응답에는 시간(UTC)과 함께 파티션의 RestoreEpoch 및 RestordLSN이 제공됩니다. 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __실패__ - 복원 상태가 _실패_이면 복원 요청이 실패한 것입니다. 실패 이유는 요청에 설명됩니다.
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __시간 제한__ - 복원 상태가 _시간 제한_이면 요청 시간이 초과된 것입니다. [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)을 높여서 복원 요청을 새로 하는 것이 좋으며, 기본 시간 제한은 10분입니다. 복원을 다시 요청하기 전에 파티션이 데이터 손실 상태에서 벗어났는지 확인하는 것이 좋습니다.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>자동 복원

Service Fabric 클러스터의 Reliable Stateful 서비스 및 Reliable Actors에 대한 파티션에 _자동 복원_을 구성할 수 있습니다. 백업 정책을 만들 때 `AutoRestore`를 _true_로 설정할 수 있습니다.  파티션에 _자동 복원_을 사용하도록 설정하면 데이터 손실이 보고될 때 최신 백업에서 데이터가 복원됩니다.
 
 [백업 정책에서 자동 복원을 사용하도록 설정](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>다음 단계
- [정기 백업 구성 이해](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [백업 복원 REST API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
