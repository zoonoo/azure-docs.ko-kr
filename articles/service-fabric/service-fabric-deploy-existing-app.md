<properties
   pageTitle="Azure 서비스 패브릭에 기존 실행 파일 배포 | Microsoft Azure"
   description="Azure 서비스 패브릭 클러스터에 배포할 수 있도록 기존 응용 프로그램을 패키지 하는 방법에 대한 연습"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>
   
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="bscholl"/>

# 서비스 패브릭에 게스트 실행 파일 배포

Azure 서비스 패브릭에서 Node.js, Java 또는 네이티브 응용 프로그램과 같은 모든 유형의 응용 프로그램을 실행할 수 있습니다. 서비스 패브릭 용어에서는 이러한 종류의 응용 프로그램을 게스트 실행 파일이라고 부릅니다. 게스트 실행 파일은 서비스 패브릭에서 상태 비저장 서비스처럼 취급됩니다. 결과적으로 클러스터의 노드에 배치되며, 가용성 및 기타 메트릭을 기반으로 합니다. 이 문서에서는 서비스 패브릭 클러스터에 게스트 실행 파일을 패키징 및 배포하는 방법을 설명합니다.

## 서비스 패브릭에서 게스트 실행 파일을 실행할 때의 이점

서비스 패브릭 클러스터에서 게스트 실행 파일을 실행하면 몇 가지 장점이 있습니다.

- 고가용성. 서비스 패브릭에서 실행되는 응용 프로그램은 항상 바로 사용할 수 있습니다. 서비스 패브릭은 응용 프로그램의 한 인스턴스가 항상 실행되도록 보장합니다.
- 상태 모니터링. 기본적으로 서비스 패브릭 상태 모니터링은 응용 프로그램이 실행 중인지 감지하고 오류가 발생할 경우 진단 정보를 제공합니다.   
- 응용 프로그램 수명 주기 관리. 서비스 패브릭은 가동 중지 없이 업그레이드가 가능할 뿐 아니라 업그레이드하는 동안 문제가 발생하면 이전 버전으로 롤백할 수 있습니다.    
- 밀도. 한 클러스터에서 여러 응용 프로그램을 실행할 수 있으므로 응용 프로그램을 고유의 하드웨어에서 실행할 필요가 없습니다.

이 문서에서는 게스트 실행 파일을 패키징하고 서비스 패브릭에 배포하는 기본 단계를 다룹니다.


## 응용 프로그램 및 서비스 매니페스트 파일의 간략한 개요

게스트 실행 파일 배포의 세부 정보로 들어가기 전에 서비스 패브릭 패키징 및 배포 모델을 이해하는 것이 유용합니다. 서비스 패브릭 패키징 배포 모델은 주로 두 파일을 사용합니다.


* **응용 프로그램 매니페스트**

  응용 프로그램 매니페스트는 응용 프로그램을 설명하는 데 사용되며 응용 프로그램을 구성하는 서비스와 서비스 배포 방법(예: 인스턴스 수)을 정의하는 데 사용되는 기타 매개 변수를 나열합니다.

  서비스 패브릭 세계에서 응용 프로그램은 "업그레이드 가능한 단위"입니다. 잠재적인 오류(및 잠재적 롤백)가 플랫폼에 의해 관리되는 하나의 단위로 응용 프로그램을 업그레이드할 수 있습니다. 플랫폼은 업그레이드 프로세스의 성공을 보장하며, 업그레이드가 실패할 경우 응용 프로그램을 알 수 없는/불안정한 상태로 남겨 두지 않습니다.


 ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationManifest ApplicationTypeName="actor2Application"
                       ApplicationTypeVersion="1.0.0.0"
                       xmlns="http://schemas.microsoft.com/2011/01/fabric"
                       xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest>
  ```

* **서비스 매니페스트**

  서비스 매니페스트는 서비스의 구성 요소를 설명합니다. 서비스 매니페스트는 서비스의 이름 및 유형(서비스 패브릭이 서비스 관리에 사용하는 정보), 서비스 코드, 구성 및 데이터 구성 요소를 포함하고 있습니다. 또한 서비스 매니페스트는 서비스가 배포되면 서비스를 구성하는 데 사용할 수 있는 몇 가지 추가 매개 변수도 포함하고 있습니다.

  서비스 매니페스트에서 사용할 수 있는 모든 매개 변수를 자세히 다루지는 않고, 게스트 실행 파일을 서비스 패브릭에서 실행하는 데 필요한 하위 집합을 살펴보겠습니다.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ServiceManifest Name="actor2Pkg"
                   Version="1.0.0.0"
                   xmlns="http://schemas.microsoft.com/2011/01/fabric"
                   xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <ServiceTypes>
      <StatelessServiceType ServiceTypeName="actor2Type" />
    </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest>
  ```

## 응용 프로그램 패키지 파일 구조
응용 프로그램을 서비스 패브릭에 배포하기 위해 응용 프로그램은 미리 정의된 디렉터리 구조를 따라야 합니다. 다음은 해당 구조의 예입니다.

```
|-- ApplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

루트는 응용 프로그램을 정의하는 ApplicationManifest.xml 파일을 포함합니다. 응용 프로그램에 포함된 각 서비스의 하위 디렉터리는 서비스에 필요한 모든 아티팩트(ServiceManifest.xml)를 포함하는 데 사용되며 일반적으로 다음 세 개 디렉터리입니다.

- *Code*. 이 디렉터리는 서비스 코드를 포함합니다.
- *Config*. 이 디렉터리는 런타임에 서비스가 액세스하여 특정 구성 설정을 검색할 수 있는 settings.xml 파일(필요한 경우 다른 파일도 포함)을 포함합니다.
- *Data*. 서비스에 필요할 수도 있는 추가 로컬 데이터를 저장하는 추가 디렉터리입니다. 참고: Data 폴더는 사용 후 삭제되는 데이터를 저장하는 용도로만 사용해야 합니다. 예를 들어 장애 조치(failover) 중에 서비스를 다시 배치해야 하는 경우 서비스 패브릭은 변경 내용을 데이터 디렉터리에 복사/복제하지 않습니다.

참고: `config` 및 `data` 디렉터리가 필요 없으면 만들지 않아도 됩니다.

## 기존 앱을 패키징하는 과정

게스트 실행 파일을 패키징하는 과정은 다음 단계를 기반으로 합니다.

1. 패키지 디렉터리 구조를 만듭니다.
2. 응용 프로그램의 코드 및 구성 파일을 추가합니다.
3. 서비스 매니페스트 파일을 편집합니다.
4. 응용 프로그램 매니페스트 파일을 편집합니다.

>[AZURE.NOTE] ApplicationPackage를 자동으로 만들 수 있는 패키징 도구가 제공됩니다. 이 도구는 현재 미리 보기로 제공되고 있습니다. [여기](http://aka.ms/servicefabricpacktool)에서 다운로드할 수 있습니다.

### 패키지 디렉터리 구조 만들기
앞에서 설명한 것처럼 디렉터리 구조를 만들어서 시작할 수 있습니다.

### 응용 프로그램의 코드 및 구성 파일 추가
디렉터리 구조를 만든 후에 응용 프로그램의 코드 및 구성 파일을 코드 및 구성 디렉터리 아래에 추가할 수 있습니다. 코드 또는 구성 디렉터리 아래에 하위 디렉터리 또는 추가 디렉터리를 만들 수도 있습니다.

서비스 패브릭은 응용 프로그램 루트 디렉터리의 내용에 대한 xcopy를 수행하므로 두 상위 디렉터리인 code 및 settings를 만들지 않고도 사용할 수 있는 미리 정의된 구조가 없습니다. 하지만 원한다면 다른 이름을 선택할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

>[AZURE.NOTE] 응용 프로그램이 필요한 모든 파일/종속성을 포함했는지 확인합니다. 서비스 패브릭은 응용 프로그램의 서비스를 배포할 클러스터의 모든 노드에서 응용 프로그램 패키지의 콘텐츠를 복사합니다. 패키지에는 응용 프로그램 실행에 필요한 모든 코드가 있어야 합니다. 종속성이 이미 설치되어 있다고 가정하는 것은 좋지 않습니다.

### 서비스 매니페스트 파일 편집
다음 정보를 포함하도록 서비스 매니페스트 파일을 편집하는 것이 다음 단계입니다.

- 서비스 형식 이름 서비스 패브릭이 서비스를 식별하기 위해 사용하는 ID입니다.
- 응용 프로그램을 시작하기 위해 사용하는 명령(ExeHost).
- 응용 프로그램을 설치/구성하기 위해 실행해야 하는 모든 스크립트(SetupEntrypoint)

아래는 `ServiceManifest.xml` 파일의 예입니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

업데이트해야 하는 파일의 다른 부분을 살펴보겠습니다.

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- `ServiceTypeName`의 이름을 자유롭게 선택할 수 있습니다. 이 값은 `ApplicationManifest.xml` 파일에서 서비스를 식별하는 데 사용됩니다.
- `UseImplicitHost="true"`를 지정해야 합니다. 이 특성은 서비스가 자체 포함된 앱을 기반으로 하므로 모든 서비스 패브릭이 응용 프로그램을 프로세스로 실행하여 상태를 모니터링해야 한다는 사실을 서비스 패브릭에 전달합니다.

### CodePackage
CodePackage 요소는 서비스 코드의 위치(및 버전)을 지정합니다.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 요소는 서비스 코드가 들어 있는 응용 프로그램 패키지의 디렉터리 이름을 지정하는 데 사용됩니다. `CodePackage`에도 `version` 특성이 있습니다. 이 요소는 코드 버전을 지정하는 데 사용할 수 있으며, 서비스 패브릭의 응용 프로그램 수명 주기 관리 인프라를 사용하여 서비스 코드를 업그레이드하는 데 사용할 수도 있습니다.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint 요소는 서비스의 코드를 시작하기 전에 실행되어야 하는 실행 파일 또는 배치 파일을 지정하는 데 사용됩니다. 선택적 요소이므로 초기화/설치가 필수가 아닌 경우에는 포함되지 않아도 됩니다. SetupEntrypoint는 서비스를 다시 시작할 때마다 실행됩니다.

SetupEntrypoint가 하나밖에 없으므로 응용 프로그램의 설치/구성에 여러 스크립트가 필요한 경우 설치/구성 스크립트를 배치 파일 하나에 번들로 묶여야 합니다. Entrypoint 요소와 마찬가지로, SetupEntrypoint는 실행 파일, 배치 파일, PowerShell cmdlet 등 각종 파일을 실행할 수 있습니다. 위의 예에서 SetupEntrypoint는 코드 디렉터리의 `scripts` 하위 디렉터리에 있는 배치 파일(LaunchConfig.cmd)을 기반으로 합니다(WorkingDirectory 요소가 Code로 설정되었다고 가정).

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

서비스 매니페스트 파일의 `Entrypoint` 요소는 서비스를 시작하는 방법을 지정하는 데 사용됩니다. `ExeHost` 요소는 서비스를 시작하는 데 사용되어야 하는 실행 파일(및 인수)을 지정합니다.

- `Program`은 서비스를 시작하기 위해 실행되어야 하는 실행 파일의 이름을 지정합니다.
- `Arguments`는 실행 파일에 전달되어야 하는 인수를 지정합니다. 인수가 있는 매개 변수 목록이 될 수도 있습니다.
- `WorkingFolder`는 곧 시작될 프로세스의 작업 디렉터리를 지정합니다. 두 값을 지정할 수 있습니다.
	- `CodeBase`는 작업 디렉터리가 응용 프로그램 패키지의 코드 디렉터리(아래 표시된 구조의 `Code` 디렉터리)에 설정되도록 지정합니다.
	- `CodePackage`는 작업 디렉터리가 응용 프로그램 패키지(`MyServicePkg`)의 루트로 설정되도록 지정합니다.
- `WorkingDirectory`는 응용 프로그램 또는 초기화 스크립트에서 상대 경로를 사용할 수 있도록 올바른 작업 디렉터리를 설정하는 데 유용합니다.

### 끝점

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
`Endpoint` 요소는 응용 프로그램에서 수신할 수 있는 끝점을 지정합니다. 이 예제에서 Node.js 응용 프로그램은 포트 3000에서 수신합니다.

## 응용 프로그램 매니페스트 파일 편집

`Servicemanifest.xml` 파일을 구성했으면 `ApplicationManifest.xml` 파일을 변경하여 올바른 서비스 유형 및 이름이 사용되는지 확인해야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

`ServiceManifestImport` 요소에서 앱에 포함할 서비스를 하나 이상 지정할 수 있습니다. 서비스는 `ServiceManifest.xml` 파일이 있는 디렉터리의 이름을 지정하는 `ServiceManifestName`으로 참조됩니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### 로깅 설정
게스트 실행 파일의 경우 응용 프로그램 및 구성 스크립트가 오류를 표시할 때 콘솔 로그를 볼 수 있으므로 매우 유용합니다. 콘솔 리디렉션은 `ConsoleRedirection` 요소를 사용하여 `ServiceManifest.xml` 파일에 구성할 수 있습니다.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`은 서비스 패브릭 클러스터의 응용 프로그램의 설치 또는 실행 중에 오류가 없음을 확인하는 데 사용할 수 있도록 콘솔 출력(stdout 및 stderr 모두)을 작업 디렉터리로 리디렉션하는 데 사용될 수 있습니다.

	* `FileRetentionCount`는 작업 디렉터리에 저장되는 파일의 수를 결정합니다. 예를 들어 값이 5이면 이전 5개 실행에 대한 로그 파일이 작업 디렉터리에 저장되었다는 뜻입니다.
	* `FileMaxSizeInKb`는 로그 파일의 최대 크기를 지정합니다.

로그 파일은 서비스의 작업 디렉터리 중 하나에 저장됩니다. 파일이 어디에 있는지 확인하려면 서비스 패브릭 탐색기를 사용하여 서비스가 실행 중인 노드와 현재 사용 중인 작업 디렉터리를 확인해야 합니다. 이 프로세스는 이 문서의 뒷부분에서 다루겠습니다.

### 배포
마지막 단계는 응용 프로그램을 배포하는 것입니다. 아래의 PowerShell 스크립트는 로컬 개발 클러스터에 응용 프로그램을 배포하고 새 서비스 패브릭 서비스를 시작하는 방법을 보여 줍니다.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
서비스 패브릭 서비스를 다양한 "구성"으로 배포할 수 있습니다. 예를 들어 단일 또는 다중 인스턴스로 배포할 수도 있고, 서비스 패브릭 클러스터의 각 노드에 서비스 인스턴스가 하나씩 있는 방식으로 배포할 수도 있습니다.

`New-ServiceFabricService` cmdlet의 `InstanceCount` 매개 변수는 서비스 패브릭 클러스터에서 실행되어야 하는 서비스의 인스턴스 개수를 지정하는 데 사용됩니다. 배포하는 응용 프로그램의 유형에 따라 `InstanceCount` 값을 설정할 수 있습니다. 가장 일반적인 두 가지 시나리오는 다음과 같습니다.

* `InstanceCount = "1"`. 이 경우 단 한 개의 서비스 인스턴스가 클러스터에 배포됩니다. 서비스 패브릭의 스케줄러는 서비스를 배포할 노드를 결정합니다.

* `InstanceCount ="-1"`. 이 예에서는 서비스의 단일 인스턴스가 서비스 패브릭 클러스터의 모든 노드에 배포됩니다. 최종 결과로 클러스터의 각 노드에 대한 서비스의 인스턴스를 하나(및 하나만) 갖게 됩니다.

이 구성은 프런트 엔드 응용 프로그램(예: REST 끝점)에 매우 유용합니다. 클라이언트 응용 프로그램은 끝점을 사용하려면 클러스터의 노드에 "연결"해야 하기 때문입니다. 또한 서비스 패브릭 클러스터의 모든 노드가 부하 분산 장치에 연결되어 있어서 클라이언트 트래픽을 클러스터의 모든 노드에서 실행되는 서비스로 분산할 수 있는 경우에도 이 구성을 사용할 수 있습니다.

### 실행 중인 응용 프로그램 확인

서비스 패브릭 탐색기에서 서비스가 실행되고 있는 노드를 식별합니다. 이 예제에서는 Node1에서 실행됩니다.

![서비스가 실행 중인 노드](./media/service-fabric-deploy-existing-app/runningapplication.png)

노드로 이동하여 응용 프로그램을 찾으면 디스크 상의 위치를 포함하여 필수 노드 정보가 표시됩니다.

![디스크 상의 위치](./media/service-fabric-deploy-existing-app/locationondisk.png)

서버 탐색기를 사용하여 디렉터리로 이동하면 아래와 같이 작업 디렉터리 및 서비스의 로그 폴더를 찾을 수 있습니다.

![로그 위치](./media/service-fabric-deploy-existing-app/loglocation.png)


## 다음 단계
이 문서에서는 게스트 실행 파일을 패키징하고 서비스 패브릭에 배포하는 방법을 배웠습니다. 다음 단계로 이 항목에 대한 추가 콘텐츠를 확인할 수 있습니다.

- 패키징 도구 시험판의 링크를 포함하여 [GitHub에 게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication)
- [여러 개의 게스트 실행 파일 배포](service-fabric-deploy-multiple-apps.md)
- [Visual Studio를 사용하여 처음으로 서비스 패브릭 응용 프로그램 만들기](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0218_2016-->