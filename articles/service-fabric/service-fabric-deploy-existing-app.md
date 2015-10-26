<properties
   pageTitle="Azure 서비스 패브릭에서 기존 응용 프로그램 배포 | Microsoft Azure"
   description="Azure 서비스 패브릭 클러스터에 배포할 수 있도록 기존 응용 프로그램을 패키지 하는 방법에 대한 연습"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Azure 서비스 패브릭에서 기존 응용 프로그램 배포

Azure 서비스 패브릭은 상태 모니터링 및 응용 프로그램 수명 주기 관리(ALM)의 이점을 사용할 수 있도록 하는 기존 응용 프로그램 배포에 사용할 수 있습니다.

이 자습서에서는 기존 응용 프로그램 사용과 서비스 패브릭 클러스터에 배포를 위한 패키지와 관련된 프로세스 및 기본 개념을 설명합니다.


## 응용 프로그램 및 서비스 매니페스트 파일의 간략한 개요

기존 응용 프로그램 배포의 세부 정보로 들어가기 전에 서비스 패브릭 배포 모델을 이해하는 것이 유용합니다. 서비스 패브릭 배포 모델은 주로 두 파일을 사용합니다.


* **응용 프로그램 매니페스트**

  응용 프로그램 매니페스트는 응용 프로그램을 설명하는 데 사용되며 구성된 서비스 및 인스턴스 수와 같은 기타 매개변수를 나열하며, 서비스가 배포되는 방법을 정의합니다. 서비스 패브릭 세계에서 응용 프로그램은 '업그레이드 가능한 단위'입니다. 잠재적인 오류(및 잠재적 롤백)를 플랫폼에서 관리하여 업그레이드 프로세스가 성공하거나 실패한 경우 응용 프로그램이 알 수 없음/불안정한 상태가 되도록 할 수 있는 하나의 단위로 응용 프로그램을 업그레이드할 수 있습니다.

  다음은 응용 프로그램 매니페스트의 예입니다.

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

  서비스 매니페스트는 서비스의 구성 요소를 설명합니다. 서비스의 이름 및 유형(서비스 패브릭에서 서비스를 관리하는 데 사용하는 정보), 해당 코드, 구성 및 데이터 구성 요소와 배포되면 서비스를 구성하는 데 사용할 수 있는 일부 추가 매개 변수 등의 데이터를 포함합니다. 서비스 매니페스트에서 사용할 수 있는 모든 다른 매개 변수의 정보로 이동하지 않으며 서비스 패브릭에서 기존 응용 프로그램을 실행하는 데 필요한 하위 집합으로 이동합니다.

  서비스 매니페스트의 예입니다.

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
Powershell cmdlet 등을 사용하는 응용 프로그램을 배포하기 위해 응용 프로그램은 미리 정의된 디렉터리 구조를 따라야 합니다.

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

루트는 응용 프로그램을 정의하는 applicationmanifest.xml 파일을 포함합니다. 응용 프로그램에 포함된 각 서비스에 대한 하위 디렉터리 서비스는 서비스가 필요한 모든 아티팩트를 포함하는 데 사용됩니다. servicemanifest.xml 및 일반적으로 3개의 디렉터리를 포함합니다.

- *code*: 서비스 코드를 포함합니다.
- *config*: 런타임에 서비스에서 특정 구성 설정을 검색하도록 액세스할 수 있는 settings.xml 파일(및 필요한 경우 다른 파일)을 포함합니다.
- *data*: 서비스가 필요할 수 있는 추가 로컬 데이터를 저장하는 추가 디렉터리입니다. 참고: 데이터는 ephymeral 데이터만을 저장하는 데 사용되어야 하며, 예를 들어, 장애 조치 중에 서비스를 다시 배치해야 하는 경우 서비스 패브릭은 변경 내용을 데이터 디렉터리에 복사/복제하지 않습니다.

참고: 코드, 구성 및 데이터에 대한 모든 임의의 디렉터리 이름을 사용할 수 있습니다. ApplicationManifest 파일에서 동일한 값을 사용하는지 확인합니다.

## 기존 앱을 패키징하는 과정

기존 응용 프로그램을 패키징하는 과정은 다음 단계를 기반으로 합니다.

- 패키지 디렉터리 구조 만들기
- 응용 프로그램의 코드 및 구성 파일 추가
- 서비스 매니페스트 파일 업데이트
- 응용 프로그램 매니페스트 업데이트


### 패키지 디렉터리 구조 만들기
위에서 설명한 것처럼 디렉터리 구조를 만들어서 시작할 수 있습니다. 디렉터리를 만들고 이전에 Visual Studio에서 만든 기존 프로젝트에서 응용 프로그램 및 서비스 매니페스트를 복사합니다.

![][1] ![][2]


### 응용 프로그램의 코드 및 구성 파일 추가
디렉터리 구조를 만든 후에 응용 프로그램의 코드 및 구성 파일을 코드 및 구성 디렉터리 아래에 추가할 수 있습니다. 코드 또는 구성 디렉터리 아래에 하위 디렉터리 또는 추가 디렉터리를 만들 수도 있습니다. 서비스 패브릭은 응용 프로그램 루트 디렉터리의 내용에 대한 xcopy를 수행하므로 두 상위 디렉터리 코드 및 설정 작성 이외에 사용할 미리 정의된 구조가 없습니다(하지만 원할 경우 서로 다른 이름을 선택할 수 있습니다. 자세한 내용은 다음 섹션에 제공됩니다.).

>[AZURE.NOTE]\: 응용 프로그램이 필요한 모든 파일/종속성을 포함했는지 확인합니다. 패브릭 서비스는 응용 프로그램의 서비스를 배포할 클러스터의 모든 노드에서 응용 프로그램 패키지의 콘텐츠를 복사합니다. 패키지에는 응용 프로그램 실행에 필요한 모든 코드가 있어야 합니다. 종속성이 이미 설치되어 있다고 가정하는 것은 좋지 않습니다.

### 서비스 매니페스트 파일 편집
다음 정보를 포함하도록 서비스 매니페스트 파일을 편집하는 것이 다음 단계입니다.

- 서비스 형식 이름 서비스를 식별하기 위해 서비스 패브릭이 사용하는 'ID'입니다.
- 응용 프로그램(ExeHost)을 시작하기 위해 사용하는 명령
- 응용 프로그램(SetupEntrypoint)을 설치/구성하기 위해 실행되어야 하는 스크립트

다음은 node.js 응용 프로그램을 '패키지'하는 `servicemanifest.xnml` 파일의 예입니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

업데이트해야 하는 파일의 다른 부분을 살펴보겠습니다.

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- `ServiceTypeName`에 대해 원하는 이름을 임의로 선택할 수 있습니다. 값은 `applicationmanifest.xml`에 사용되어 서비스를 식별합니다.
- `UserImplicitHost = "true"`를 지정해야 합니다. 이 특성은 서비스 패브릭에게 서비스가 자체 포함된 앱을 기반으로 하므로 프로세스로 응용 프로그램을 시작하고 해당 상태를 모니터링하면 됨을 알려줍니다.

### CodePackage
CodePackage는 서비스의 코드 위치(및 버전)을 지정합니다.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 요소는 서비스의 코드를 포함하는 응용 프로그램 패키지의 디렉터리 이름을 지정하는 데 사용됩니다. `CodePackage`에는 코드의 버전을 지정하는 데 사용할 수 있으며, 서비스 패브릭의 ALM 인프라를 사용하여 잠재적으로 서비스의 코드를 업그레이드하는 데 사용될 수 있는 `version` 특성도 있습니다.


### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


서비스 매니페스트 파일의 `Entrypoint` 요소는 서비스를 시작하는 방법을 지정하는 데 사용됩니다. 서비스를 시작하는 데 사용되어야 하는 `ExeHost` 요소는 실행 파일(및 인수)을 지정합니다.

- `Program`: 서비스를 시작하기 위해 실행되어야 하는 실행 파일의 이름을 지정합니다.
- `Arguments`: 실행 파일에 전달되어야 하는 인수를 지정합니다. 인수가 있는 매개 변수의 목록일 수 있습니다.
- `WorkingFolder`: 시작하고자 하는 프로세스에 대한 작업 디렉터리를 지정합니다. 두 값을 지정할 수 있습니다.
	- `CodeBase`: 작업 디렉터리는 응용 프로그램 패키지의 코드 디렉터리에 설정됩니다(아래 표시된 구조의 `Code` 디렉터리).
	- `CodePackage`: 작업 디렉터리가 응용 프로그램 패키지(`MyServicePkg`)의 루트로 설정됩니다.
- `WorkingDirectory` 요소는 응용 프로그램 또는 초기화 스크립트에서 상대 경로를 사용할 수 있도록 올바른 작업 디렉터리를 설정하는 데 유용합니다.

`WorkingFolder` 요소(`Work`)에 지정할 수 있는 다른 값도 있지만 기존 응용 프로그램으로 전환하는 시나리오에 매우 유용하지 않습니다.


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### 설치 프로그램 진입점

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

`SetupEntrypoint`는 서비스의 코드를 시작하기 전에 실행되어야 하는 실행 파일 또는 배치 파일을 지정하는 데 사용됩니다. 선택적 요소이므로 필요한 초기화/설치가 없는 경우 포함되지 않아도 됩니다. Entrypoint은 서비스를 다시 시작할 때마다 실행됩니다. 하나의 SetupEntrypoint가 있으므로 응용 프로그램의 설치/구성에 여러 스크립트가 필요한 경우 설치/구성 스크립트를 단일 일괄처리 파일에서 번들로 묶여야 합니다. `Entrypoint` 요소와 마찬가지로, `SetupEntrypoint`는 실행 파일, 배치 파일, powershell cmdlet 등의 각종 파일을 실행할 수 있습니다. 위의 예에서 `SetupEntrypoint`는 코드 디렉터리의 스크립트 하위 디렉터리에 있는 배치 파일(myAppsetup.cmd)을 기반으로 합니다(`WorkingDirectory` 요소가 `Code`로 설정되었다고 가정).

## 응용 프로그램 매니페스트 파일

`servicemanifest.xml` 파일을 구성하면 `applicationmanifest.xml` 파일에 대한 일부 변경 내용을 작성하여 올바른 서비스 유형 및 이름이 사용되는지 확인해야 합니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

`ServiceManifestImport`에서 앱에 포함하려는 하나 이상의 서비스를 지정할 수 있습니다. 서비스는 `servicemanifest.xml` 파일이 있는 디렉터리의 이름을 지정하는 `ServiceManifestName`으로 참조됩니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

응용 프로그램 매니페스트 파일의 `DefaultServices` 요소는 일부 서비스 속성을 정의하는 데 사용됩니다.

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName`은 서비스에 대한 'Id'로 사용됩니다. 기존 응용 프로그램 이식의 컨텍스트에서 `ServiceTypeName`은 서비스에 대해 고유 ID여야 합니다.
* `StatelessService`: 서비스 패브릭은 상태 비저장 및 상태 저장의 두 유형의 서비스를 지원합니다. 기존 응용 프로그램을 이식하는 경우, 서비스는 상태 비저장 서비스이므로 `StatelessService`가 항상 사용되어야 합니다.

서비스 패브릭 서비스는 다양한 '구성'으로 배포될 수 있습니다. 예를 들어, 서비스 패브릭 클러스터의 각 노드에 서비스의 단일 인스턴스가 있는 방식으로 단일 또는 여러 인스턴스로 배포될 수 있습니다. `applicationmanifest.xml` 파일에서 응용 프로그램을 배포할 방법을 지정할 수 있습니다.

* `InstanceCount`: 서비스 패브릭 클러스터에서 실행되어야 하는 서비스의 인스턴스 개수를 지정하는 데 사용됩니다. 배포하는 응용 프로그램의 유형에 따라 `InstanceCount` 값을 설정할 수 있습니다. 가장 일반적인 두가지 시나리오는 다음과 같습니다.

	* `InstanCount = "1"`: 이 경우 단 한 개의 서비스 인스턴스가 클러스터에 배포됩니다. 서비스 패브릭의 스케줄러는 배포할 서비스 노드를 결정합니다. 단일 인스턴스 개수는 여러 인스턴스에서 실행되는 경우 다른 구성이 필요한 응용 프로그램에 대해서도 사용됩니다. 이 경우 동일한 응용 프로그램 매니페스트 파일에서 더 쉽게 여러 서비스를 정의하고 `InstanceCount = "1"`을 사용할 수 있습니다. 따라서 최종 결과에는 동일한 서비스의 여러 인스턴스가 있지만 각각은 특정 구성을 사용합니다. 정확히 동일한 구성의 여러 인스턴스를 갖는 것이 목표일 경우에만 1보다 큰 `InstanceCount`의 값이 사용됩니다.

	* `InstanceCount ="-1"`: 단 한 개의 서비스 인스턴스가 서비스 패브릭 클러스터의 모든 노드에 배포됩니다. 최종 결과로 클러스터의 각 노드에 대한 서비스의 인스턴스를 하나(및 하나만) 갖게 됩니다. 끝점을 사용하도록 클라이언트 응용 프로그램이 클러스터의 노드에 '연결'해야 하기 때문에 프런트엔드 응용 프로그램(예: REST 끝점)에 유용한 구성입니다. 예를 들어, 서비스 패브릭 클러스터의 모든 노드가 부하 분산 장치에 연결된 경우 이 구성을 사용할 수도 있으므로, 클라이언트 트래픽을 클러스터의 모든 노드에서 실행되는 서비스에 분산될 수 있습니다.


### 테스트
기존 응용 프로그램이 콘솔 응용 프로그램 및 구성 스크립트가 오류를 표시하지 않는 경우를 찾기 위해 콘솔 로그를 볼 수 있게 하는 데 매우 유용합니다. 콘솔 리디렉션은 `ConsoleRedirection` 요소를 사용하여 `servicemanifest.xml` 파일에서 구성될 수 있습니다.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`은 서비스 패브릭 클러스터의 응용 프로그램의 설치 또는 실행 중에 오류가 없음을 확인하는 데 사용할 수 있도록 콘솔 출력(stdout 및 stderr 모두)을 작업 디렉터리로 리디렉션하는 데 사용될 수 있습니다.

	* `FileRetentionCount`는 작업 디렉터리에 저장되는 파일의 수를 결정합니다. 예를 들어, 5의 값은 이전 5 실행에 대한 로그 파일이 작업 디렉터리에 저장됨을 의미합니다.
	* `FileMaxSizeInKb`는 로그 파일의 최대 크기를 지정합니다.

로그 파일은 서비스의 작업 디렉터리 중 하나에 저장되며, 파일의 위치를 확인하려면, 서비스 패브릭 탐색기를 사용하여 서비스가 실행 중인 노드 및 현재 사용된 작업 디렉터리를 확인해야 합니다.

서비스 패브릭 탐색기에서 서비스가 실행되고 있는 노드를 식별합니다.

![][3]

서비스가 현재 실행 중인 노드를 알면 사용된 작업 디렉터리를 찾을 수 있습니다.

![][4]

서비스의 이름을 선택하는 경우 오른쪽 패널에서 서비스 코드 및 설정이 저장되어 있음을 알 수 있습니다.

![][5]

'디스크 위치' 필드에 대한 링크를 클릭하면 서비스에서 실행되고 있는 디렉터리에 액세스할 수 있습니다.

![][6]

로그 디렉터리는 모든 로그 파일을 포함합니다.

참고: 이 예제에서는 클러스터에서 실행되는 서비스의 단일 인스턴스의 대/소문자를 보여줍니다. 여러 인스턴스가 있을 경우 서비스가 실행되고 있는 모든 노드에서 로그 파일을 확인해야 합니다.


## 다음 단계
앱의 디렉터리 구조 루트에서 선택하여 기존 응용 프로그램을 패키지하는 데 사용될 수 있는 도구에서 작업합니다. 도구는 매니페스트 파일을 생성하고 서비스 패브릭 서비스의 응용 프로그램을 '변환'하는 데 필요한 기본 설정 구성을 처리합니다.

기존의 서비스 패브릭 앱을 개발하는 방법에 대한 자세한 내용을 보려면 [여기](service-fabric-develop-your-service-index.md)를 참조하세요.

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO3-->