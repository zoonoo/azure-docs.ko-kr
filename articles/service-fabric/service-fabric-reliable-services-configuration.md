<properties
   pageTitle="Azure 서비스 패브릭 Reliable Services 구성의 개요 | Microsoft Azure"
   description="Azure 서비스 패브릭에서 상태 저장 Reliable Services를 구성하는 방법을 알아봅니다."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/29/2016"
   ms.author="sumukhs"/>

# 상태 저장 신뢰할 수 있는 서비스 구성
구성 패키지(구성) 또는 서비스 구현(코드)을 사용하여 상태 저장 신뢰할 수 있는 서비스의 기본 구성을 수정할 수 있습니다.

+ **구성** - 구성 패키지를 통한 구성은 응용 프로그램의 각 서비스에 대한 Microsoft Visual Studio 패키지 루트의 Config 폴더에 생성된 Settings.xml 파일을 변경하여 수행됩니다.
+ **코드** - 코드를 통한 구성은 StatefulService.CreateReliableStateManager를 재정의하고 적절한 옵션 집합을 가진 ReliableStateManagerConfiguration 개체를 사용하여 ReliableStateManager를 만들어 수행됩니다.

기본적으로 Azure 서비스 패브릭 런타임은 settings.xml 파일에서 미리 정의된 섹션 이름을 찾아서 기본 런타임 구성 요소를 만드는 동안 해당 구성 값을 사용합니다.

>[AZURE.NOTE] 코드를 통해 서비스를 구성할 예정이 아니면 Visual Studio 솔루션에서 생성된 "Settings.xml" 파일에서 다음 구성의 섹션 이름을 삭제하지 **않도록** 합니다. ReliableStateManager를 구성할 때 구성 패키지 또는 섹션의 이름을 바꾸려면 코드를 변경해야 합니다.


## 복제자 보안 구성
복제자 보안 구성은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 따라서 서비스는 서로의 복제 트래픽을 볼 수 없으므로 항상 사용 가능하게 설정한 데이터를 안전하게 보호할 수 없습니다. 기본적으로 빈 보안 구성 섹션에서는 복제 보안이 되지 않습니다.

### 기본 섹션 이름
ReplicatorSecurityConfig

>[AZURE.NOTE] 이 섹션의 이름을 변경하려면 이 서비스에 대한 ReliableStateManager를 만들 때 replicatorSecuritySectionName 매개 변수를 ReliableStateManagerConfiguration 생성자로 재정의합니다.


## 복제자 구성
복제자 구성은 상태를 로컬로 복제하고 유지하여 상태 저장 신뢰할 수 있는 서비스의 상태를 매우 안정적으로 만드는 일을 담당하는 복제자를 구성합니다. 기본 구성은 Visual Studio 템플릿에 의해 생성되며 충분해야 합니다. 이 섹션에서는 복제자 조정에 사용할 수 있는 추가 구성에 대해 설명합니다.

### 기본 섹션 이름
ReplicatorConfig

>[AZURE.NOTE] 이 섹션의 이름을 변경하려면 이 서비스에 대한 ReliableStateManager를 만들 때 replicatorSettingsSectionName 매개 변수를 ReliableStateManagerConfiguration 생성자로 재정의합니다.


### 구성 이름
|이름|단위|기본값|설명|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|초|0\.05|작업을 수신한 후 주 복제본에 대한 승인을 다시 보내기 전에 보조 복제본의 복제자가 대기하는 시간. 이 간격 내에서 처리하는 작업에 대해 보낼 나머지 승인은 모두 하나의 응답으로 전송됩니다.|
|ReplicatorEndpoint|해당 없음|기본값 없음--필수 매개 변수|주/보조 복제자가 복제본 세트의 다른 복제자와 통신하는 데 사용할 IP 주소 및 포트. 서비스 매니페스트의 TCP 리소스 끝점을 참조해야 합니다. 서비스 매니페스트에서 끝점 리소스를 정의하는 방법에 대한 자세한 내용은 [서비스 매니페스트 리소스](service-fabric-service-manifest-resources.md)를 참조하세요. |
|MaxPrimaryReplicationQueueSize|작업의 수|8192|기본 큐의 최대 작업 수. 작업은 주 복제자가 모든 보조 복제자로부터 승인을 받은 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다.|
|MaxSecondaryReplicationQueueSize|작업의 수|16384|보조 큐의 최대 작업 수. 작업은 지속성을 통해 상태를 항상 사용 가능하도록 설정한 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다.|
|CheckpointThresholdInMB|MB|50|상태가 검사점이 된 후의 로그 파일 공간 크기|
|MaxRecordSizeInKB|KB|1024|복제자가 로그에 기록할 수 있는 최대 레코드 크기. 이 값은 4의 배수이고 16보다 커야 합니다.|
|OptimizeLogForLowerDiskUsage|Boolean|true|True일 경우 NTFS 스파스 파일을 사용하여 복제본의 전용 로그 파일을 만들도록 로그가 구성됩니다. 이렇게 하면 파일의 실제 디스크 공간 사용이 줄어듭니다. False일 경우 파일이 고정 할당으로 생성되고, 최상의 쓰기 성능을 제공합니다.|
|MaxRecordSizeInKB|KB|1024|복제자가 로그에 기록할 수 있는 최대 레코드 크기. 이 값은 4의 배수이고 16보다 커야 합니다.|
|SharedLogId|GUID|""|이 복제본과 함께 사용되는 공유 로그 파일을 식별하는 데 사용할 고유한 GUID를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 됩니다. 그러나 SharedLogId가 지정된 경우 SharedLogPath도 지정해야 합니다.|
|SharedLogPath|정규화된 경로 이름|""|이 복제본의 공유 로그 파일을 생성할 정규화된 경로를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 됩니다. 그러나 SharedLogPath가 지정된 경우 SharedLogId도 지정해야 합니다.|


## 코드를 통한 샘플 구성
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


## 샘플 구성 파일
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


## 설명
BatchAcknowledgementInterval은 복제 대기 시간을 제어합니다. '0' 값은 처리량을 희생하여 가장 낮은 대기 시간을 제공합니다(더 많은 승인 메시지를 보내고 처리해야 하므로 각각에 포함된 승인은 적음). BatchAcknowledgementInterval의 값이 클수록 전체적인 복제 처리량은 높아지고 작업 대기 시간은 더욱 길어집니다. 이 값은 트랜잭션 커밋의 대기 시간으로 직접 변환됩니다.

CheckpointThresholdInMB 값은 복제자가 복제자의 전용 로그 파일에 상태 정보를 저장하는 데 사용할 수 있는 디스크 공간의 크기를 제어합니다. 새 복제본을 집합에 추가할 때 기본값보다 더 높은 값으로 늘리면 재구성 시간을 단축할 수 있습니다. 이는 로그의 더 많은 작업 기록을 사용함으로써 상태 전송이 부분적으로 발생하기 때문입니다. 이로 인해 크래시 후 복제본의 복구 시간이 증가할 수 있습니다.

OptimizeForLowerDiskUsage를 true로 설정하면 활성 복제본이 로그 파일에 더 많은 상태 정보를 저장하고 비활성 복제본이 디스크 공간을 덜 사용하도록 로그 파일 공간이 오버프로비전됩니다. 이렇게 하면 더 많은 복제본을 노드에 호스트할 수 있습니다. OptimizeForLowerDiskUsage를 false로 설정하면 로그 파일에 상태 정보가 더 빠르게 기록됩니다.

MaxRecordSizeInKB 설정은 복제자가 로그 파일에 쓸 수 있는 레코드의 최대 크기를 정의합니다. 대부분의 경우 기본 1024KB 레코드 크기가 최적입니다. 그러나 서비스에서 더 큰 데이터 항목이 상태 정보의 일부가 되도록 할 경우 이 값을 늘려야 할 수 있습니다. MaxRecordSizeInKB를 1024보다 작은 값으로 설정하면 작은 레코드만 작은 레코드에 필요한 공간을 사용하므로 이점이 적습니다. 이 값은 드문 경우에만 변경되어야 합니다.

SharedLogId 및 SharedLogPath 설정은 항상 함께 사용되며 서비스가 노드에 대한 기본 공유 로그에서 별도의 공유 로그를 사용하도록 합니다. 최상의 효율성을 위해 최대한 많은 서비스가 동일한 공유 로그를 지정해야 합니다. 공유 로그 파일에만 사용되는 디스크에 공유 로그 파일을 배치해야 헤드 이동 경합이 감소합니다. 이 값은 드문 경우에만 변경되어야 합니다.

<!---HONumber=AcomDC_0309_2016-->