<properties
   pageTitle="Reliable Services 백업 및 복원 | Microsoft Azure"
   description="서비스 패브릭 Reliable Services 백업 및 복원에 관한 개념 설명서"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="mcoskun"/>

# Reliable Services 백업 및 복원

Azure 서비스 패브릭은 고가용성 플랫폼으로, 여러 노드에 걸쳐 상태를 복제하여 고가용성을 유지합니다. 따라서 클러스터의 한 노드에서 오류가 발생해도 서비스를 지속적으로 사용할 수 있습니다. 많은 경우 플랫폼에서 제공하는 이러한 기본 제공 중복성으로 충분하지만 어떤 경우에는 서비스를 위해 (외부 저장소에) 데이터를 백업하는 것이 바람직합니다.

예를 들어, 다음과 같은 시나리오에서 서비스의 데이터 백업이 필요할 수 있습니다.

* 특정 파티션에서 실행되는 모든 노드 또는 전체 서비스 패브릭 클러스터가 영구 손실된 경우.

* 상태가 우발적으로 삭제되거나 손상된 관리 오류. 예를 들어 충분한 권한이 있는 관리자가 실수로 서비스를 삭제한 경우에 발생합니다.

* 데이터 손상을 초래하는 서비스 내 버그. 예를 들어 신뢰할 수 있는 컬렉션에 잘못된 데이터 작성을 시작하는 서비스 코드가 업그레이드되는 경우 발생할 수 있습니다. 이런 경우 코드와 데이터 모두 이전 상태로 되돌아가야 할 수도 있습니다.

* 오프라인 데이터 처리. 데이터를 생성하는 서비스와는 별도로 비즈니스 인텔리전스를 위한 데이터를 오프라인 처리하는 것이 편리할 수 있습니다.

백업/복원 기능을 사용하면 Reliable Services API에 구축된 서비스로 백업을 만들고 복원할 수 있습니다. 플랫폼에서 제공하는 백업 API를 사용하면 읽기나 쓰기 작업을 차단하지 않고 파티션 상태의 백업을 수행할 수 있습니다. 복원 API는 선택한 백업에서 파티션의 상태를 복원할 수 있습니다.


## 백업 작업

서비스 작성자에는 백업 수행 시기와 백업 저장 위치에 대한 모든 권한이 있습니다.

백업을 시작하려면 서비스가 **IReliableStateManager.BackupAsync**를 호출해야 합니다. 백업은 주 복제본에서만 수행되며 상태 쓰기 권한을 부여해야 합니다.

아래와 같이 **BackupAsync**의 가장 간단한 오버로드는 **backupCallback**이라고 하는 Func<< BackupInfo  bool >>를 받아들입니다.

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo**는 백업과 관련한 정보를 제공합니다. 여기에는 런타임이 백업을 저장한 폴더의 위치가 포함됩니다.(**BackupInfo.Directory**) 콜백 함수는 **BackupInfo.Directory**를 외부 저장소 또는 다른 위치로 이동합니다. 이 함수는 백업 폴더를 대상 위치로 이동할 수 있는지 여부를 표시하는 부울 값도 반환합니다.

다음 코드는 backupCallback을 사용하여 Azure 저장소에 백업을 업로드하는 방법을 보여줍니다.

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

위 예에서 **ExternalBackupStore**는 Azure Blob 저장소와의 인터페이스에 사용되는 샘플 클래스이며 **UploadBackupFolderAsync**는 폴더를 압축하여 Azure Blob 저장소에 배치하는 메서드입니다.

다음 사항에 유의하세요.

- 특정 시점에서 처리 중인 복제본 당 하나의 **BackupAsync**만 존재할 수 있습니다. 동시에 여러 **BackupAsync**가 호출되면 처리 중인 백업을 하나로 제한하는 **FabricBackupInProgressException**이 발생합니다.

- 백업 진행 중에 복제본 장애 조치가 발생하면 백업이 완료되지 않을 수 있습니다. 따라서 장애 조치(failover)가 완료된 후 서비스가 필요에 따라 **BackupAsync**를 호출하여 백업을 다시 시작해야 합니다.

## 복원 작업

일반적으로 복원 작업을 수행해야 하는 경우는 다음 범주 중 하나에 속합니다.


- 서비스 파티션에서 데이터가 손실되었습니다. 예를 들어, 파티션에 대한 세 복제본 중 두 복제본(주 복제본 포함)에 대한 디스크가 손상되거나 초기화되었습니다. 새 주 복제본이 백업에서 데이터를 복원해야 할 수도 있습니다.

- 전체 서비스가 손실되었습니다. 예를 들어, 관리자가 전체 서비스를 제거하여 서비스와 데이터를 복원해야 합니다.

- 서비스가 손상된 응용 프로그래 데이터를 복제했습니다.(예: 응용 프로그램 버그 때문에) 이 경우 손상 원인을 제거하기 위해 서비스를 업그레이드하거나 되돌려야 하며 손상되지 않은 데이터를 복원해야 합니다.

다양한 접근 방법이 가능하지만 위의 시나리오에서 **RestoreAsync**를 사용하여 복원하는 몇 가지 예만 설명하겠습니다.

## 파티션 데이터 손실

이 경우 런타임이 자동으로 데이터 손실을 탐지하고 **OnDataLossAsync** API를 호출합니다.

서비스 작성자는 복구하려면 다음을 수행해야 합니다.

- **CreateReliableStateManager**를 재정의하여 새 **ReliableStateManager**를 반환하고 데이터 손실 이벤트의 경우 호출될 호출 함수를 제공합니다.

- 서비스의 백업을 포함하는 외부 위치에서 최신 백업을 찾습니다.

- 최신 백업 상태가 새로운 기본 복제본보다 낮은 경우 false를 반환합니다. 이렇게 하면 새로운 기본 복제본은 오래된 데이터로 덮어쓰여 지지 않습니다.

- 최신 백업을 다운로드(및 압축된 경우 백업에서 백업 폴더에 압축 해제)합니다.

- 백업 폴더의 경로를 사용하여 **IReliableStateManager.RestoreAsync**를 호출합니다.

- 복원이 성공하면 true를 반환합니다.

다음은 **IReliableStateManager** 재정의가 있는 **OnDataLossAsync** 메서드의 예제 구현입니다.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] RestorePolicy는 기본적으로 Safe로 설정됩니다. 즉, 백업 폴더에 이 복제본에 포함된 상태와 같거나 그 이전인 상태가 포함되었음을 탐지한 경우 **RestoreAsync** API가 ArgumentException과 함께 실패합니다. **RestorePolicy.Force**를 사용하여 이 보안 검사를 건너뛸 수 있습니다.

## 서비스 삭제 또는 손상

서비스가 제거된 경우 데이터 복원에 앞서 서비스를 다시 만들어야 합니다. 데이터의 원할한 복원을 위해 파티션 구성표 등과 같은 동일한 구성의 서비스를 만드는 것이 좋습니다. 서비스가 실행되면 서비스의 모든 파티션에서 데이터 복원을 위한 API(위의 **OnDataLossAsync**)를 호출해야 합니다. 이를 수행하기 위한 한 가지 방법은 모든 파티션에서 **FabricClient.ServiceManager.InvokeDataLossAsync**를 사용하는 것입니다.

이 시점부터는 앞의 시나리오와 같은 방식으로 구현됩니다. 각 파티션이 외부 저장소로부터 최신 관련 백업을 복원해야 합니다. 한 가지 주의할 점은 런타임이 동적으로 파티션 ID를 생성하기 대문에 파티션 ID가 변경될 수 있다는 사실입니다. 따라서 서비스가 각 파티션에서 복원할 정확한 최신 백업을 식별할 수 있게 적합한 파티션 정보 및 서비스 이름을 저장해야 합니다.


## 손상된 응용 프로그램 데이터의 복제

새로 배포된 응용 프로그램 업그레이드에 버그가 있는 경우 데이터 손상을 초래할 수 있습니다. 응용 프로그램 업그레이드를 시작하여 잘못된 지역 번호로 신뢰할 수 있는 사전의 모든 전화번호 레코드를 업데이트하는 경우를 예로 들 수 있습니다. 이 경우 서비스 패브릭이 저장하는 데이터의 본질을 인지하지 못하므로 잘못된 전화 번호가 복제됩니다.

이렇게 데이터 손상을 초래할 수 있는 황당한 버그를 탐지한 후 가장 먼저 할 일은 응용 프로그램 수준에서 서비스를 고정하고, 가능하다면 버그가 없는 응용 프로그램 코드의 버전으로 업그레이드하는 것입니다. 그러나 서비스 코드가 고정된 후에도 데이터 손상이 지속되어 데이터 복원이 필요할 수 있습니다. 이 경우에는 최신 백업도 손상되었을 수 있기 때문에 최신 백업을 복원하는 것으로는 부족할 수 있습니다. 따라서 데이터 손상 이전에 만든 최신 백업을 찾아야 합니다.

백업이 손상되었는지 확실하지 않은 경우 새 서비스 패브릭 클러스터를 배포하고 위의 "삭제되거나 손상된 서비스" 시나리오와 마찬가지로 영향을 받는 파티션의 백업을 복원할 수 있습니다. 각 파티션에 대해 가장 최근부터 가장 오래된 순으로 백업을 복원하기 시작합니다. 손상되지 않은 백업을 찾은 후 이 파티션에 있는 모든 백업(해당 백업보다 최신 버전)을 이동/삭제합니다. 각 파티션에 대해 이 프로세스를 반복합니다. 이제 프로덕션 클러스터의 파티션에서 **OnDataLossAsync**가 호출되며 위의 프로세스에 따라 외부 저장소에서 찾은 최신 백업이 선택됩니다.

이제 "삭제되거나 손상된 서비스" 섹션의 단계를 사용하여 버그 코드로 인한 손상 이전의 상태로 서비스 백업 상태를 복원할 수 있습니다.

다음 사항에 유의하세요.

- 복원할 때 복원 중인 백업이 데이터 손실 이전의 파티션 상태보다 오래된 것일 가능성이 있습니다. 이 때문에 가능한 많은 데이터를 복구하기 위한 마지막 수단으로만 복원해야 합니다.

- 백업 폴더 경로와 백업 폴더 내 파일 경로를 나타내는 문자열은 FabricDataRoot 경로 및 응용 프로그램 형식 이름의 길이에 따라 255자보다 길 수 있습니다. 이로 인해 **Directory.Move** 등과 같은 일부 .NET 메서드에서 **PathTooLongException** 예외가 발생할 수 있습니다. 한 가지 해결 방법은 **CopyFile**과 같은 kernel32 API를 직접 호출하는 것입니다.


## 내부 살펴보기: 백업 및 복원에 대 한 자세한 내용

### 백업
Reliable State Manager는 읽기 및 쓰기 작업을 차단하지 않고 일관된 백업을 만드는 기능을 제공합니다. 이를 위해 검사점 및 로그 지속성 메커니즘을 활용합니다. Reliable State Manager는 트랜잭션 로그로부터의 부담을 줄이고 복구 시간을 단축하기 위해 특정 지점에서 유사 항목(경량) 검사점을 사용합니다. IReliableStateManager.**BackupAsync**가 호출되면 Reliable State Manager가 모든 신뢰 개체에게 최신 검사점 파일을 로컬 백업 폴더에 복사하도록 지시합니다. 그런 다음 Reliable State Manager가 "시작 포인터"에서부터 마지막 로그 레코드까지의 모든 로그 레코드를 백업 폴더에 복사합니다. 최신 로그 레코드까지의 모든 로그 레코드가 백업에 포함되며 Reliable State Manager가 미리 쓰리 로깅을 유지하므로 Reliable State Manager는 커밋된 모든 트랜잭션(**CommitAsync**가 성공적으로 결과를 반환함)이 백업에 포함되도록 보장합니다.

**BackupAsync**가 호출된 이후에 커밋된 모든 트랜잭션은 백업에 포함되거나 포함되지 않을 수 있습니다. 로컬 백업 폴더를 플랫폼에서 입력합 후에는(즉, 런타임에서 로컬 백업 완료됨) 서비스의 백업 콜백이 호출됩니다. 이 콜백은 백업 폴더를 Azure 저장소 등의 외부 위치로 이동하는 것을 담당합니다.

### 복원

Reliable State Manager는 **IReliableStateManager.RestoreAsync** API를 사용하여 백업으로부터 복원하는 기능을 제공합니다. **RestoreAsync** 메서드는 **OnDataLossAsync** 메서드 내에서만 호출할 수 있습니다. **OnDataLossAsync**가 반환한 부울 값은 서비스가 외부 원본으로부터 상태를 복원했는지 여부를 나타냅니다. **OnDataLossAsync**가 True를 반환할 경우 서비스 패브릭이 이 기본 복제본에서 다른 모든 복제본을 다시 빌드합니다. 서비스 패브릭은 **OnDataLossAsync**를 받는 복제본이 먼저 주 역할로 전환되지만 읽기 상태 또는 쓰기 상태는 부여되지 않도록 합니다. 즉, **OnDataLossAsync**가 성공적으로 완료될 때까지는 StatefulService 구현자에 대해 **RunAsync**가 호출되지 않습니다. 그런 다음 **OnDataLossAsync**가 새 기본 복제본에서 호출됩니다. 서비스가 이 API를 성공적으로 완료하고(True 또는 False 반환) 관련 재구성을 마치면 한 번에 하나씩 API가 계속 호출됩니다.


**RestoreAsync**는 먼저 호출된 기본 복제본에서 모든 기존 상태를 삭제합니다. 이후 Reliable State Manager가 백업 폴더에 존재하는 모든 신뢰 개체를 만듭니다. 다음으로 백업 폴더의 검사점으로부터 백업하도록 신뢰 개체에게 지시합니다. 마지막으로 Reliable State Manager가 백업 폴더의 로그 레코드에서 자체 상태를 복구하고 복구를 수행합니다. 복구 프로세스의 일환으로 백업 폴더에서 로그 레코드를 커밋한 "시작점"에서 시작하는 작업이 신뢰 개체에 재현됩니다. 이 단계를 통해 일관된 복구 상태를 유지합니다.

<!---HONumber=AcomDC_0309_2016-->