<properties
   pageTitle="Node.js 및 신뢰할 수 있는 행위자 | Microsoft Azure"
   description="신뢰할 수 있는 행위자를 사용하고 Azure 서비스 패브릭 플랫폼을 기반으로 실행하는 node.js Express 응용 프로그램을 작성하는 방법을 안내합니다."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# Node.js 및 신뢰할 수 있는 행위자: 우세한 조합
이 문서는 node.js 및 신뢰할 수 있는 행위자를 사용하는 응용 프로그램을 구축하는 방법에 대한 개요입니다. 최종 솔루션은 더 복잡한 계산을 위해 앱(웹/Rest API) 및 C#의 프런트 엔드 측면을 제공하는 데 주로 사용되는 javascript 코드의 혼합입니다. 서비스 패브릭 프로그래밍 모델을 활용하여 응용 프로그램은 확장 가능하고 바로 안정적입니다. 프로세스는 다음 단계에 기반합니다.

1. Visual Studio에 대한 서비스 패브릭 도구를 사용하여 새 서비스 패브릭 상태 비저장 또는 저장 작업 자 프로젝트 만들기
2. 예를 들어 [Visual Studio용 node.js 도구](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC)를 사용하여 node.js 프로젝트 만들기 
3. 서비스 패브릭 솔루션에 node.js 프로젝트(예를 들어 기본 Express 4개 앱)를 추가합니다. 단순히 솔루션을 사용/기존 프로젝트를 추가하여 node.js 프로젝트를 포함할 수 있습니다. 
4. node.js 앱을 패키지하여 서비스 패브릭용 VS 도구를 사용하여 배포할 수 있습니다.

## node.js 프로젝트 만들기
node.js 프로젝트를 만들고 종속성을 추가한 후에 프로젝트의 디렉터리 구조를 변경하므로 다른 서비스 패브릭 서비스처럼 앱을 포장 및 배포할 수 있도록 Visual Studio용 서비스 패브릭 도구가 필요한 구조를 따릅니다. 목표가 한 번만 디렉터리 구조를 변경하는 것이므로 Visual Studio에서 배포 프로세스를 사용하는 혜택을 얻을 수 있고 따라서 솔루션의 다른 서비스와 함께 node.js 앱을 배포할 수 있습니다. 디렉터리 구조에 작업을 수행하는 변경 내용은 다음과 같습니다.

1. PackageRoot 디렉터리 만들기
2. PackageRoot에서 코드 디렉터리 만들기
3. 코드 디렉터리의 모든 파일 및 디렉터리 이동

수행한 후에 Visual Studio의 프로젝트 구조는 다음과 같습니다.

![][8]

보시는 바와 같이 node.js 코드는 PackageRoot/코드 디렉터리에 있습니다. 서비스 패브릭은 응용 프로그램이 배포될 노드에 설치된 응용 프로그램/라이브러리에 대한 가정을 하지 않으므로 모든 종속성을 포함합니다. Node.js의 경우 서비스 패브릭이 코드를 실행할 수 있도록 node.exe를 포함해야 합니다.(node.exe는 program files\\nodejs 디렉터리에서 찾을 수 있음)

![][3]

## Servicemanifest.xml 메타데이터 파일 추가
Node.js 응용 프로그램을 배포하려면 응용 프로그램을 배포하고 시작하는 방법을 결정하기 위해 서비스 패브릭에서 사용할 일부 속성을 지정하는 데 필요한 메타데이터 파일을 추가해야 합니다.

servicemanifest.xml가 표시되는 예제입니다. 업데이트해야 하지만 일반적으로 업데이트되어야 하는 중요한 요소에 대한 자세한 내용은 [이 문서](service-fabric-deploy-existing-app.md)를 참조하세요.

* 이름(ServiceManifest 요소)
* ServiceTypeName(StatelessServiceType 요소)
* 인수(ExeHost 요소)로 앱을 시작하는 데 사용할 js 파일을 지정합니다.


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
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
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]servicemanifest.xml 옵션은 node.js과 함께 작동하지 않을 수 있습니다. 경우에 따라 `ConsoleRedirection`은 작동하지 않습니다.(예: Express) 이 경우 node.js 모듈은 stdout 및 stderr에 대한 fd이 1 및 2라고 가정합니다.

`servicemanifest.xml` 파일이 node.js 프로젝트에 추가된 후에 프로젝트의 구조는 다음과 같아야 합니다.

![][4]

## 서비스 패브릭 이진 파일 추가
다음 단계는 서비스 패브릭 클러스터에서 실행되는 행위자와 연결하는 데 사용하는 서비스 패브릭 이진 파일을 추가합니다. Edge.js 샘플에서 볼 수 있듯이 어셈블리에 대한 참조가 있습니다. edge.js에 이러한 어셈블리를 사용할 수 있도록 node.js 코드와 함께 복사되어야 합니다. 가장 쉬운 방법은 기존 프로젝트에서 이진 파일을 복사하는 것입니다. 코드 디렉터리에 포함(및 edge.js에서 사용)되어야 하는 파일은 다음과 같습니다.

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

이진 파일을 프로젝트에 추가한 후에 프로젝트 구조는 다음과 같아야 합니다.

![][5]

## Applicationmanifest 파일에서 node.js 프로젝트에 대한 참조를 추가합니다.
다음 단계는 응용 프로그램 매니페스트에 node.js 서비스를 추가하므로 서비스 패브릭용 Visual Studio 도구를 사용하여 배포할 수 있습니다. node.js 프로젝트와 함께 Visual Studio용 서비스 패브릭 도구에서 통합되지 않기 때문에 필요하므로 수동으로 추가해야 합니다.

`applicationmanifest.xml` 파일에 다음 요소를 추가해야 합니다.

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]`servicemanifest.xml`에서 사용된 같은 이름을 사용하는지 확인하여 `ServiceName` 및 `ServiceTypeName`을 지정합니다. `applicationmanifest.xml` 파일은 다음과 유사해야 합니다.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Node.js 앱에서 신뢰할 수 있는 행위자 사용
프로젝트 구조를 설정했으므로 node.js 응용 프로그램을 사용하는 코드에 집중하여 .NET 응용 프로그램에서 수행할 수 있는 클러스터의 신뢰할 수 있는 행위자를 연결할 수 있습니다. 사용하려는 시나리오는 클라이언트 응용 프로그램에 대한 프런트 엔드/게이트웨이 역할을 하는 node.js 응용 프로그램을 빌드하고 클라이언트 앱이 사용할 수 있는 웹 기반 응용 프로그램 또는 rest API 집합을 노출합니다. 신뢰할 수 있는 행위자가 확장성 및 안정성 있는 앱의 '응용 프로그램 서버' 계층을 제공하는 반면 Node.js는 앱의 프런트 엔드를 제공합니다. 서비스 패브릭에서 [ActorProxy 클래스](service-fabric-reliable-actors-introduction.md#actor-communication)를 사용하여 신뢰할 수 있는 행위자를 호출할 수 있습니다. 다행히 .NET 코드에 호출하는 데 사용할 수 있는 node.js 모듈이 있습니다.[Edge.js](https://github.com/tjanczuk/edge). github 리포지토리에 지침을 사용하여 컴퓨터 가장자리에 설치하는 방법을 알아볼 수 있습니다.

 
![][2]

Visual Studio에서 NPM 또는 NPM UI 중 하나를 사용하여 가장자리를 설치한 경우 코드 하위 디렉터리의 node\_modules 디렉터리에 설치된 새 모듈을 이동해야 합니다.(node.js 프로젝트의 구조를 변경 및 PackageRoot/코드 디렉터리 아래의 모든 코드를 이동) Github의 edge.js 리포지토리에서 가장자리를 사용하여 .NET 코드를 호출하는 좋은 예를 찾을 수 있습니다. 다음은 ActorProxy 클래스를 사용하여 신뢰할 수 있는 행위자를 호출해야 하는 경우 코드가 표시되는 간단한 예제입니다.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy는 연결해야 하는 작업 자를 확인하기 위해 작업자 인터페이스를 사용합니다. 해당 클래스를 인스턴스화할 수 있는 node.js 프로세스에서 실행되는 관리 코드를 위해 행위자 인터페이스 어셈블리를 다른 서비스 패브릭 dll과 같은 코드 디렉터리에 복사해야 합니다. 참고: 인터페이스에서 메서드/매개 변수를 변경하려면 언제든 dll을 복사(또는 VS에서 빌드 후 작업을 설정)해야 합니다.

![][6]

## 배포
이때 Visual Studio용 서비스 패브릭 도구를 사용하여 프로젝트를 배포할 수 있습니다. 프로세스의 마지막 단계가 서비스 패브릭 응용 프로그램 프로젝트에서 프로젝트를 참조하므로 응용 프로그램 패키지에 포함되고 클러스터에 배포될 수 있습니다.

![][9]
 
예를 들어 솔루션 컨텍스트 메뉴를 사용하여 응용 프로그램(node.js 앱 포함)을 배포할 수 있습니다.

![][10]

## 다음 단계
* [신뢰할 수 있는 행위자](service-fabric-reliable-actors-introduction.md)에 대해 자세히 알아보기
* 서비스 패브릭 [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아보기
* [서비스 패브릭 응용 프로그램](service-fabric-application-upgrade.md) 업그레이드에 대해 자세히 알아보기 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Oct15_HO3-->