---
title: Azure Service Fabric의 정기 백업 구성 이해 | Microsoft Docs
description: Service Fabric의 주기적 백업 및 복원 기능을 사용하여 애플리케이션 데이터의 주기적인 데이터 백업을 사용하도록 설정합니다.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: b1b36ed5197aeb056c70200a49e09cc777d66d0b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237349"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Azure Service Fabric의 정기 백업 구성 이해

Reliable Stateful 서비스 또는 Reliable Actors에 대한 주기적인 백업 구성은 다음 단계로 이루어집니다.

1. **백업 정책 만들기**: 이 단계에서는 요구 사항에 따라 하나 이상의 백업 정책이 만들어집니다.

2. **백업 활성화**: 이 단계에서는 **1단계**에서 만든 백업 정책을 필요한 엔터티, _애플리케이션_, _서비스_ 또는 _파티션_과 연결합니다.

## <a name="create-backup-policy"></a>Backup 정책 만들기

백업 정책은 다음 구성으로 이루어집니다.

* **데이터 손실 시 자동 복원**: 파티션에 데이터 손실 이벤트가 발생할 경우 사용 가능한 최신 백업을 사용하여 복원을 자동으로 트리거할지 여부를 지정합니다.

* **최대 증분 백업**: 두 개의 전체 백업 간에 수행할 증분 백업의 최대 수를 정의합니다. 최대 증분 백업은 상한을 지정합니다. 다음 중 한 가지 조건에서, 지정된 수의 증분 백업이 완료되기 전에 전체 백업이 수행될 수 있습니다.

    1. 복제본이 주 복제본이 된 이후로 전체 백업을 수행한 적이 없습니다.

    2. 마지막 백업 이후 일부 로그 레코드가 잘렸습니다.

    3. 복제본이 MaxAccumulatedBackupLogSizeInMB 제한을 통과했습니다.

* **백업 일정**: 정기적인 백업을 수행할 시간 또는 빈도입니다. 지정된 간격으로 되풀이되거나 매일/매주 정해진 시간에 되풀이되도록 백업을 예약할 수 있습니다.

    1. **빈도 기반 백업 일정**: 고정된 간격으로 데이터를 백업해야 하는 경우 이 일정 유형을 사용해야 합니다. 연속적인 두 개의 백업 간에 원하는 간격은 ISO8601 형식을 사용하여 정의됩니다. 빈도 기반 백업 일정은 간격을 분 단위로 지원합니다.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **시간 기반 백업 일정**: 하루 또는 일주일의 특정 시간에 데이터를 백업해야 하는 경우 이 일정 유형을 사용해야 합니다. 일정 빈도 유형은 매일이거나 매주일 수 있습니다.
        1. **_매일_ 시간 기반 백업 일정**: 하루 중 특정 시간에 데이터를 백업해야 하는 경우 이 일정 유형을 사용해야 합니다. 이것을 지정하려면 `ScheduleFrequencyType`을 _매일_로 설정하고 `RunTimes`을 하루 중 원하는 시간 목록(ISO8601 형식)으로 설정합니다. 시간과 함께 지정된 날짜는 무시됩니다. 예를 들어 `0001-01-01T18:00:00`은 매일 _오후 6시_를 나타내며 날짜 부분인 _0001-01-01_은 무시됩니다. 아래 예제는 매일 _오전 9시_와 _오후 6시_에 매일 백업을 트리거하는 구성을 보여줍니다.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **_매주_ 시간 기반 백업 일정**: 하루 중 특정 시간에 데이터를 백업해야 하는 경우 이 일정 유형을 사용해야 합니다. 이것을 지정하려면 `ScheduleFrequencyType`을 _매주_로 설정하고 `RunDays`는 백업을 트리거해야 하는 일주일 요일 목록으로 설정하고 `RunTimes`는 하루 중 원하는 시간 목록(ISO8601 형식)으로 설정합니다. 시간과 함께 지정된 날짜는 무시됩니다. 정기적인 백업을 트리거할 요일을 나열합니다. 아래 예제는 월요일부터 금요일까지 _오전 9시_와 _오후 6시_에 매일 백업을 트리거하는 구성을 보여줍니다.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **백업 스토리지**: 백업을 업로드할 위치를 지정합니다. 저장소는 Azure Blob 저장소 또는 파일 공유일 수 있습니다.
    1. **Azure blob 저장소**: 생성된 백업을 Azure에 저장할 필요가 있는 경우 스토리지 유형을 선택해야 합니다. 독립 실행형(_standalone_) 저장소와 Azure 기반(_Azure-based_) 클러스터 모두 이 저장소 유형을 사용할 수 있습니다. 이 저장소 유형에 대한 설명에는 연결 문자열과 백업을 업로드해야 하는 컨테이너 이름이 필요합니다. 지정된 이름의 컨테이너를 사용할 수 없으면 백업 업로드 중에 만들어집니다.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **파일 공유**: 데이터 백업을 온-프레미스에 저장해야 하는 경우 _독립 실행형_ 클러스터에 대해 이 스토리지 유형을 선택해야 합니다. 이 저장소 유형에 대한 설명에는 백업을 업로드해야 하는 파일 공유 경로가 필요합니다. 파일 공유에 대한 액세스는 다음 옵션 중 하나를 사용하여 구성할 수 있습니다.
        1. Windows 통합 인증의 경우, 파일 공유에 대한 액세스가 Service Fabric 클러스터에 속하는 모든 컴퓨터에 제공됩니다.  이 경우 다음 필드를 설정하여 파일 공유 기반 백업 저장소를 구성합니다. 

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. 사용자 이름과 암호를 사용하여 파일 공유 보호의 경우, 파일 공유에 대한 액세스가 Service Fabric 클러스터에 속하는 모든 컴퓨터에 제공됩니다.  파일 공유 저장소 사양은 주 사용자 이름 및 주 암호로 인증이 실패할 경우 대체 인증을 제공하기 위해 보조 사용자 이름 및 보조 암호를 지정하는 기능도 제공합니다. 이 경우 다음 필드를 설정하여 파일 공유 기반 백업 저장소를 구성합니다. 

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> 저장소 안정성이 백업 데이터의 안정성 요구 사항을 충족하거나 초과하는지 확인해야 합니다.
>

* **보존 정책**: 구성된 스토리지에 백업을 보존할 정책을 지정합니다. 기본 보존 정책만 지원됩니다.
    1. **기본 보존 정책**: 이 보존 정책은 더 이상 필요하지 않은 백업 파일을 제거하여 최적의 스토리지 사용률을 보장합니다. `RetentionDuration`은 백업을 스토리지에 보존해야 하는 시간 범위를 설정하도록 지정할 수 있습니다. `MinimumNumberOfBackups`는 `RetentionDuration`와 관계 없이 항상 지정된 수의 백업이 보존되도록 지정할 수 있는 선택적 매개 변수입니다. 아래 예제에서는 _10_일 동안 백업을 보존하는 구성을 보여 주며 백업 수가 _20_ 미만으로 떨어지는 것을 허용하지 않습니다.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>정기적 백업 사용
데이터 백업 요구 사항을 충족하도록 백업 정책을 정의한 후에는 백업 정책이 애플리케이션이나 서비스 또는 파티션과 적절하게 연결되어야 합니다.   

### <a name="hierarchical-propagation-of-backup-policy"></a>백업 정책의 계층적 전파
Service Fabric에서 애플리케이션, 서비스 및 파티션 간의 관계는 [애플리케이션 모델](./service-fabric-application-model.md).에서 설명한대로 계층적입니다. 백업 정책은 계층 구조의 애플리케이션, 서비스 또는 파티션과 연결될 수 있습니다.    백업 정책은 다음 수준에 계층적으로 전파됩니다. 백업 정책이 하나만 생성되어 애플리케이션과 연결된다고 가정하면 모든 _Reliable Stateful_  서비스에 속하는 모든 상태 저장 파티션과 해당 애플리케이션의 _Reliable Actors_는 백업 정책을 사용하여 백업됩니다.   또는 백업 정책이 Reliable Stateful 서비스와 연결되어 있으면 모든 파티션이 백업 정책을 사용하여 백업됩니다. 

### <a name="overriding-backup-policy"></a>백업 정책 재정의
높은 빈도의 일정으로 데이터를 백업해야 하거나 다른 저장소 계정 또는 파일 공유에 백업을 보관해야 하는 경우, 특정 서비스를 제외하고 애플리케이션의 모든 서비스에 대해 백업 일정이 동일한 데이터 백업이 필요한 시나리오가 있을 수 있습니다. 백업 복원 서비스는 서비스 및 파티션 범위에서 전파된 정책을 재정의하는 기능을 제공합니다. 백업 정책이 서비스 또는 파티션에 연결되면 전파된 백업 정책이 있는 경우 이 정책을 재정의합니다.  

### <a name="example"></a>예

이 예제에서는 _MyApp_A_와 _MyApp_B_라는 두 개의 애플리케이션을 사용한 설치를 사용합니다. _MyApp_A_ 응용 프로그램은 _SvcA1_ & _SvcA3_라는 두 개의 Reliable Stateful 서비스와 _ActorA2_라는 Reliable Actor 서비스를 포함합니다. _SvcA1_은 세 개의 파티션을 포함하지만 _ActorA2_와 _SvcA3_은 각각 두 개의 파티션을 포함합니다.  _MyApp_B_ 응용 프로그램은 _SvcB1_, _SvcB2_, _SvcB3_라는 세 개의 Reliable Stateful 서비스를 포함합니다. _SvcB1_과 _SvcB2_는 각각 두 개의 파티션을 포함하고 _SvcB3_은 세 개의 파티션을 포함합니다.

이러한 애플리케이션의 데이터 백업 요구 사항은 다음과 같다고 가정합니다.

1. MyApp_A
    1. 애플리케이션에 속하는 모든 _Reliable Stateful_ 서비스와 _Reliable Actors_의 모든 파티션에 대해 매일 백업을 만듭니다. 백업 데이터는 _BackupStore1_ 위치에 업로드합니다.

    2. 서비스 중 하나인 _SvcA3_은 매시간 데이터 백업이 필요합니다.

    3. _SvcA1_P2_ 파티션의 데이터 크기가 예상보다 커서 백업 데이터를 _BackupStore2_라는 다른 저장소 위치에 저장해야 합니다.

2. MyApp_B
    1. _SvcB1_ 서비스의 모든 파티션에 대해 일요일마다 오전 8시에 데이터 백업을 만듭니다. 백업 데이터는 _BackupStore1_ 위치에 업로드합니다.

    2. _SvcB2_P1_ 파티션에 대해 매일 오전 8시에 데이터 백업을 만듭니다. 백업 데이터는 _BackupStore1_ 위치에 업로드합니다.

이러한 데이터 백업 요구 사항을 해결하기 위해 백업 정책 BP_1~BP_5가 만들어지고 다음과 같이 백업을 사용하도록 설정합니다.
1. MyApp_A
    1. 백업 정책 _BP_1_을 빈도 기반 백업 일정으로 만들고 빈도를 24시간으로 설정합니다. 백업 저장소는 저장소 위치 _BackupStore1_을 사용하도록 구성합니다. [Enable Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup)(응용 프로그램 백업 사용) API를 사용하여 _MyApp_A_ 응용 프로그램에 이 정책을 사용하도록 설정합니다. 이 작업을 수행하면 _MyApp_A_ 애플리케이션에 속하는 _Reliable Stateful_ 서비스 및 _Reliable Actors_의 모든 파티션에 대해 백업 정책 _BP_1_을 사용하여 데이터를 백업하도록 설정됩니다.

    2. 백업 정책 _BP_2_를 빈도 기반 백업 일정으로 만들고 빈도를 1시간으로 설정합니다. 백업 저장소는 저장소 위치 _BackupStore1_을 사용하도록 구성합니다. [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup)(서비스 백업 사용) API를 사용하여 _SvcA3_ 서비스에 이 정책을 사용하도록 설정합니다. 이 작업은 _SvcA3_ 서비스의 모든 파티션에 대해 전파된 정책 _BP_1_을 명시적으로 활성화된 백업 정책 _BP_2_로 재정의하여, 해당 파티션에 대해 백업 정책 _BP_2_를 사용하여 데이터가 백업되도록 합니다.

    3. 백업 정책 _BP_3_을 빈도 기반 백업 일정으로 만들고 빈도를 24시간으로 설정합니다. 백업 저장소는 저장소 위치 _BackupStore2_를 사용하도록 구성합니다. [Enable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup)(파티션 백업 사용) API를 사용하여 _SvcA1_P2_ 파티션에 이 정책을 사용하도록 설정합니다. 이 작업은 _SvcA1_P2_ 파티션에 대해 전파된 정책 _BP_1_을 명시적으로 활성화된 백업 정책 _BP_3_으로 재정의합니다.

2. MyApp_B
    1. 백업 정책 _BP_4_를 시간 기반 백업 일정으로 만들고 일정 빈도 유형을 매주로 설정하고 실행 요일을 일요일로, 실행 시간은 오전 8시로 설정합니다. 백업 저장소는 저장소 위치 _BackupStore1_을 사용하도록 구성합니다. [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup)(서비스 백업 사용) API를 사용하여 _SvcB1_ 서비스에 이 정책을 사용하도록 설정합니다. 이 작업을 수행하면 _SvcB1_ 서비스의 모든 파티션에 대해 백업 정책 _BP_4_를 사용하여 데이터를 백업하도록 설정됩니다.

    2. 백업 정책 _BP_5_를 시간 기반 백업 일정으로 만들고 일정 빈도 유형을 매일로 설정하고 실행 시간은 오전 8시로 설정합니다. 백업 저장소는 저장소 위치 _BackupStore1_을 사용하도록 구성합니다. [Enable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup)(파티션 백업 사용) API를 사용하여 _SvcB2_P1_ 파티션에 이 정책을 사용하도록 설정합니다. 이 작업을 수행하면 _SvcB2_P1_ 파티션에 대해 백업 정책 _BP_5_를 사용하여 데이터를 백업하도록 설정됩니다.

다음 다이어그램에서는 명시적으로 활성화된 백업 정책과 전파된 백업 정책을 보여줍니다.

![Service Fabric 애플리케이션 계층 구조][0]

## <a name="disable-backup"></a>백업 사용 안 함
데이터를 백업할 필요가 없으면 백업 정책을 사용하지 않도록 설정할 수 있습니다. 애플리케이션에 사용하도록 설정된 백업 정책은 [Disable Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup)(애플리케이션 백업 사용 안 함) API를 사용하여 동일한 애플리케이션에서만 사용하지 않도록 설정할 수 있고, 서비스에 사용하도록 설정된 백업 정책은 [Disable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup)(서비스 백업 사용 안 함) API를 사용하여 동일한 서비스에서 사용하지 않도록 설정할 수 있으며 파티션에 사용하도록 설정된 백업 정책은 [Disable Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup)(파티션 백업 사용 안 함) API를 사용하여 동일한 파티션에서 사용하지 않도록 설정할 수 있습니다.      

* 애플리케이션에 대한 백업 정책을 사용하지 않도록 설정하면 Reliable Stateful 서비스 파티션이나 Reliable Actor 파티션에 백업 정책을 전파한 결과로 발생하는 정기적인 데이터 백업을 모두 중지합니다. 

* 응용 프로그램에 대한 백업 정책을 사용하지 않도록 설정하면 Reliable Stateful 서비스 파티션이나 Reliable Actor 파티션에 백업 정책을 전파한 결과로 발생하는 정기적인 데이터 백업이 모두 중지됩니다.  

* 파티션에 대한 백업 정책을 사용하지 않도록 설정하면 파티션의 백업 정책으로 인해 발생하는 정기적인 데이터 백업이 모두 중지됩니다. 

* 엔터티(애플리케이션/서비스/파티션)에 대한 백업을 사용하지 않도록 설정하는 동안 `CleanBackup`을 _true_로 설정하여 구성된 스토리지에 있는 모든 백업을 삭제할 수 있습니다.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>백업 일시 중단 및 다시 시작
데이터에 대한 정기적인 백업을 일시 중단해야 하는 특정한 상황이 있을 수 있습니다. 이런 경우 요구 사항에 따라 애플리케이션, 서비스 또는 파티션에 백업 일시 중단 API를 사용할 수 있습니다.    정기적인 백업 일시 중단은 적용되는 시점부터 애플리케이션 계층 구조의 하위 트리로 전이됩니다. 

* [Suspend Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup)(응용 프로그램 백업 일시 중단) API를 사용하여 응용 프로그램에 일시 중단이 적용되면 이 응용 프로그램의 모든 하위 서비스와 파티션에 대한 정기적인 데이터 백업이 일시 중단됩니다. 

* [Suspend Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup)(서비스 백업 일시 중단) API를 사용하여 서비스에 일시 중단이 적용되면 이 서비스의 모든 하위 파티션에 대한 정기적인 데이터 백업이 일시 중단됩니다. 

* [Suspend Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup)(파티션 백업 일시 중단) API를 사용하여 파티션에 일시 중단이 적용되면 이 서비스의 모든 하위 파티션에 대한 정기적인 데이터 백업이 일시 중단됩니다. 

일시 중단할 필요가 없어지면 각각의 백업 다시 시작 API를 사용하여 정기적인 데이터 백업을 복원할 수 있습니다. 정기적인 백업이 일시 중단된 바로 그 애플리케이션, 서비스 및 파티션에서 정기적인 백업이 다시 시작되어야 합니다.   

* 일시 중단이 애플리케이션에서 적용된 경우에는 [Resume Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup)(애플리케이션 백업 다시 시작) API를 사용하여 다시 시작해야 합니다.  

* 일시 중단이 서비스에서 적용된 경우에는 [Resume Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup)(서비스 백업 다시 시작) API를 사용하여 다시 시작해야 합니다. 

* 일시 중단이 파티션에서 적용된 경우에는 [Resume Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup)(파티션 백업 다시 시작) API를 사용하여 다시 시작해야 합니다. 

### <a name="difference-between-suspend-and-disable-backups"></a>일시 중단 및 백업 사용 안 함 간의 차이
백업 사용 안 함은 백업이 특정 애플리케이션, 서비스 또는 파티션에 더이상 필요하지 않는 경우 사용되어야 합니다. clean backups 매개 변수를 true로 설정하여 백업 사용 안 함 요청을 호출할 수 있습니다. 그러면 모든 기존 백업도 삭제됩니다. 단, 로컬 디스크가 가득 차거나 알려진 네트워크 문제 등으로 인해 백업 업로드가 실패하는 경우와 같이 일시적으로 백업을 끄려는 시나리오에서는 일시 중단이 사용됩니다. 

사용 안 함은 명시적으로 이전에 백업에 대해 사용하도록 설정되었던 수준에서만 호출할 수 있는 반면, 일시 중단은 직접 또는 상속/계층 구조를 통해 현재 백업에 대해 사용하도록 설정된 모든 수준에서 적용될 수 있습니다. 예를 들어, 애플리케이션 수준에서 백업이 사용하도록 설정된 경우 사용 안 함은 애플리케이션 수준에서만 호출될 수 있지만, 일시 중단은 해당 애플리케이션 아래의 서비스 또는 파티션에서 호출될 수 있습니다. 

## <a name="auto-restore-on-data-loss"></a>데이터 손실 시 자동 복원
예기치 않은 오류로 인해 서비스 파티션의 데이터가 손실될 수 있습니다. 예를 들어, 파티션에 대한 세 복제본 중 두 복제본(주 복제본 포함)에 대한 디스크가 손상되거나 초기화되었습니다.

파티션에서 데이터 손실이 발생한 것을 Service Fabric이 감지하면, 해당 파티션에 `OnDataLossAsync` 인터페이스 메서드를 호출하여 데이터 손실 문제를 해결하는 데 필요한 조치가 파티션에서 취해지기를 요구합니다. 이런 경우 파티션의 유효 백업 정책에 `AutoRestoreOnDataLoss` 플래그가 `true`로 설정되어 있으면, 이 파티션에 사용 가능한 최신 백업을 사용하여 복원이 자동으로 트리거됩니다.

## <a name="get-backup-configuration"></a>백업 구성 정보 가져오기
애플리케이션, 서비스 및 파티션 범위에서 백업 구성 정보를 가져올 수 있는 별도의 API가 있습니다.    [Get Application Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)(응용 프로그램 백업 구성 정보 가져오기), [Get Service Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)(서비스 백업 구성 정보 가져오기) 및 [Get Partition Backup Configuration Info](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo)(파티션 백업 구성 정보 가져오기)가 각각 여기에 해당하는 입니다. 대개, 이러한 API는 적용 가능한 백업 정책, 백업 정책이 적용되는 범위 및 백업 일시 중단 세부 정보를 반환합니다. 다음은 이러한 API가 반환한 결과에 대한 간략한 설명입니다.

- 애플리케이션 백업 구성 정보: 애플리케이션에 적용된 백업 정책과 애플리케이션에 속하는 서비스 및 파티션에서 재정의된 모든 정책에 대한 세부 정보를 제공합니다. 애플리케이션과 애플리케이션의 서비스 및 파티션에 대한 일시 중단 정보도 포함됩니다.

- 서비스 백업 구성 정보: 서비스에 유효한 백업 정책 및 이 정책이 적용된 범위 그리고 파티션에서 재정의된 모든 정책에 대한 세부 정보를 제공합니다. 서비스와 서비스의 파티션에 대한 일시 중단 정보도 포함됩니다.

- 파티션 백업 구성 정보: 파티션에 유효한 백업 정책 및 이 정책이 적용된 범위에 대한 세부 정보를 제공합니다. 파티션에 대한 일시 중단 정보도 포함됩니다.

## <a name="list-available-backups"></a>사용 가능한 백업 나열

사용 가능한 백업은 Get Backup List(백업 목록 가져오기) API를 사용하여 나열할 수 있습니다. API 호출의 결과에는 해당되는 백업 정책에 구성되어 있는 백업 저장소에서 사용 가능한 모든 백업과 관련된 백업 정보 항목이 포함됩니다. 애플리케이션, 서비스 또는 파티션에 속하는 사용 가능한 백업을 나열할 수 있도록 이 API의 다양한 변형이 제공됩니다. 이러한 API는 적용 가능한 모든 파티션의 사용 가능한 최신 백업을 가져오거나 시작 날짜와 종료 날짜를 기반으로 백업을 필터링하는 기능을 지원합니다.   

또한 이러한 API는 결과에 대한 페이지 매김을 지원하며, _MaxResults_ 매개 변수가 0이 아닌 양의 정수로 설정되면 API는 최대 _MaxResults_ 백업 정보 항목을 반환합니다. _MaxResults_ 값보다 백업 정보 항목이 많이 있는 경우에는 연속 토큰이 반환됩니다. 유효한 연속 토큰 매개 변수를 사용하면 다음 결과 집합을 가져올 수 있습니다. 유효한 연속 토큰 값이 API의 다음 호출로 전달되면 API는 다음 결과 집합을 반환합니다. 사용 가능한 결과가 모두 반환되면 연속 토큰이 응답에 포함되지 않습니다.

다음은 지원되는 변형에 대한 간략한 정보입니다.

- [애플리케이션 백업 목록 가져오기](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): 지정된 Service Fabric 애플리케이션에 속하는 모든 파티션에 사용 가능한 백업 목록을 반환합니다.

- [서비스 백업 목록 가져오기](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): 지정된 Service Fabric 서비스에 속하는 모든 파티션에 사용 가능한 백업 목록을 반환합니다.
 
- [파티션 백업 목록 가져오기](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): 지정된 파티션에 사용 가능한 백업 목록을 반환합니다.

## <a name="next-steps"></a>다음 단계
- [백업 복원 REST API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png
