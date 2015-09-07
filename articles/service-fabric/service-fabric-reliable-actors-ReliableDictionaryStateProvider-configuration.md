<properties
   pageTitle="서비스 패브릭의 신뢰할 수 있는 행위자 'ReliableDictionaryActorStateProvider' 구성의 개요"
	description="'ReliableDictionaryActorStateProvider' 형식의 서비스 패브릭 상태 저장 행위자를 구성하는 방법에 대해 알아봅니다."
	services="Service-Fabric"
	documentationCenter=".net"
	authors="sumukhs"
	manager="anuragg"
	editor=""/>

<tags
   ms.service="Service-Fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/26/2015"
	ms.author="sumukhs"/>

# 신뢰할 수 있는 행위자 구성 - ReliableDictionaryActorStateProvider
ReliableDictionaryActorStateProvider의 기본 구성은 해당 지정된 행위자에 대해 Visual Studio 패키지 루트의 "Config" 폴더에 생성된 "settings.xml" 파일을 변경하여 수정할 수 있습니다.

서비스 패브릭 런타임은 "settings.xml" 파일에서 미리 정의된 섹션 이름을 찾아서 기본 런타임 구성 요소를 만드는 동안 해당 구성 값을 사용합니다.

> [AZURE.NOTE]Visual Studio 솔루션에서 생성된 "settings.xml" 파일에서 다음 구성의 섹션 이름을 삭제/수정하지 **않도록** 합니다.

## 복제자 보안 구성
복제자 보안 구성은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 따라서 서비스는 서로의 복제 트래픽을 볼 수 없으므로 항상 사용 가능하게 설정한 데이터를 안전하게 보호할 수 없습니다. 기본적으로 빈 보안 구성 섹션에서는 복제 보안이 사용되지 않습니다.
### 섹션 이름
&lt;ActorName&gt;ServiceReplicatorSecurityConfig
### 구성 이름
[복제 보안](../service-fabric/service-fabric-replication-security.md) 참조

## 복제자 구성
복제자 구성은 상태를 로컬로 복제하고 유지하여 행위자 상태 제공자 상태를 매우 안정적으로 만드는 일을 담당하는 복제자를 구성하는 데 사용됩니다. 기본 구성은 Visual Studio 템플릿에 의해 생성되며 충분해야 합니다. 이 섹션에서는 복제자 조정에 사용할 수 있는 추가 구성에 대해 설명합니다.
### 섹션 이름
&lt;ActorName&gt;ServiceReplicatorConfig
### 구성 이름

|이름|단위|기본값|설명|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|초|0\.05|작업을 수신한 후 주 복제본에 대한 승인을 다시 보내기 전에 보조 복제본의 복제자가 대기하는 시간. 이 간격 내에서 처리하는 작업에 대해 보낼 나머지 승인은 모두 하나의 응답으로 전송됩니다.||
|ReplicatorEndpoint|해당 없음|N/A - RequiredParameter|주/보조 복제자가 복제본 세트의 다른 복제자와 통신하는 데 사용할 IP 주소 및 포트. 서비스 매니페스트의 TCP 리소스 끝점을 참조해야 합니다. 서비스 매니페스트에서 끝점 리소스를 정의하는 방법에 대한 자세한 내용은 [서비스 매니페스트 리소스](service-fabric-service-manifest-resources.md)를 참조하세요. |
|RetryInterval|초|5|작업에 대한 승인을 받지 못한 경우 복제자가 메시지를 다시 전송한 후의 시간 간격.|
|MaxReplicationMessageSize|바이트|50MB|단일 메시지에서 전송할 수 있는 복제 데이터의 최대 크기.|
|MaxPrimaryReplicationQueueSize|작업의 수|1024|기본 큐의 최대 작업 수. 작업은 주 복제자가 모든 보조 복제자로부터 승인을 받은 후 해제됩니다. 이 값은 64보다 크고 2의 제곱 값이어야 합니다.|
|MaxSecondaryReplicationQueueSize|작업의 수|2048|보조 큐의 최대 작업 수. 작업은 지속성을 통해 상태를 항상 사용 가능하도록 설정한 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다.|
|MaxStreamSizeInMB|MB|1024|예약된 로그 파일 공간의 크기입니다. 바이트 단위의 크기는 바이트 단위의 MaxRecordSize보다 16배 더 커야 합니다.|
|MaxRecordSizeInKB|KB|1024|복제자가 로그에 기록할 수 있는 최대 레코드 크기. 이 값은 4의 배수이고 16보다 커야 합니다.|
|OptimizeForLocalSSD|부울|false|True이면 상태 정보가 복제본의 전용 로그 파일에 직접 기록되도록 로그가 구성됩니다. 이는 로그 파일이 솔리드 스테이트 디스크 미디어에 있거나 VM 디스크 IO 속도가 매우 제한되어 있을 때 최상의 성능을 제공합니다. False인 경우 상태 정보가 공유 로그 파일에서 먼저 준비된 후 전용 로그 파일로 준비 취소됩니다.|
|OptimizeLogForLowerDiskUsage|부울|false|True일 경우 NTFS 스파스 파일을 사용하여 복제본의 전용 로그 파일을 만들도록 로그가 구성됩니다. 이렇게 하면 파일의 실제 디스크 공간 사용이 줄어듭니다. False일 경우 파일이 고정 할당으로 생성되고, 최상의 쓰기 성능을 제공합니다.|
|SharedLogId|GUID|""|이 복제본과 함께 사용되는 공유 로그 파일을 식별하는 데 사용할 고유한 GUID를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 되지만 SharedLogId가 지정된 경우 SharedLogPath도 지정해야 합니다.|
|SharedLogPath|정규화된 경로 이름|""|이 복제본의 공유 로그 파일을 생성할 정규화된 경로를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 되지만 SharedLogPath가 지정된 경우 SharedLogId도 지정해야 합니다.|


## 샘플 구성 파일

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

MaxStreamSizeInMB의 값은 복제자가 복제자의 전용 로그 파일에 상태 정보를 저장하는 데 사용할 수 있는 디스크 공간의 크기를 결정합니다. 저장된 정보 상태는 다른 복제본이 주 복제본의 상태를 일치시키는 데 사용되므로 일반적으로 더 큰 로그 파일을 가지는 것이 더 좋습니다. 그러면 다른 복제본이 주 복제본의 상태를 일치시키는 데 드는 시간이 줄어들기 때문입니다. 그러나 큰 로그 파일은 더 많은 디스크 공간을 사용할 수 있으므로 특정 노드에서 호스팅될 수 있는 복제본의 수를 줄이세요.

OptimizeForLowerDiskUsage 설정은 활성 복제본이 로그 파일에 더 많은 상태 정보를 저장하고, 비활성 복제본이 디스크 공간을 덜 사용하도록 로그 파일 공간의 "오버프로비저닝"을 지원합니다. 이렇게 하면 OptimizeForLowerDiskUsage를 false로 설정하여 디스크 공간 부족으로 인해 문제가 생기는 경우보다 노드에 호스팅할 수 있는 복제본은 많아지고, 로그 파일에 상태 정보가 더 빠르게 기록됩니다.

OptimizeForLocalSSD 설정은 전용 로그 파일에 대한 준비를 취소하기 전에 먼저 공유 로그 파일에 대한 상태 정보 쓰기를 비활성화하는 데 사용됩니다. 일반적으로 전용 로그 파일의 로컬 디스크 저장소가 솔리드 스테이트 미디어인 경우 디스크 헤드 이동 최소화가 문제가 되지 않기 때문에 이를 설정해야 합니다. 또한 VM이 많은 로컬 디스크 IO를 수행하고, VM 호스트에서 로컬 디스크 IO 속도를 크게 제한하는 경우에도 사용될 수 있습니다.

MaxRecordSizeInKB는 복제자가 로그 파일에 쓸 수 있는 레코드의 최대 크기를 정의합니다. 그러나 대부분의 경우 기본 1024KB 레코드 크기가 최적이지만 서비스에서 더 큰 데이터 항목이 상태 정보의 일부가 되도록 할 경우 이 값을 늘려야 할 수 있습니다. MaxRecordSizeInKB를 1024보다 작은 값으로 설정하면 작은 레코드만 작은 레코드에 필요한 공간을 사용하므로 이점이 적습니다. 이 값은 드문 경우에만 변경해야 합니다.

SharedLogId 및 SharedLogPath 설정은 항상 함께 사용되며, 서비스가 노드에 대한 기본 공유 로그에서 별도의 공유 로그를 사용하도록 허용합니다. 최상의 효율성을 위해 최대한 많은 서비스가 동일한 공유 로그를 지정해야 합니다. 헤드 이동 경합이 감소하도록 공유 로그 파일에만 사용되는 디스크에 공유 로그 파일을 배치해야 합니다. 이 값은 드문 경우에만 변경해야 합니다.
 

<!---HONumber=August15_HO9-->