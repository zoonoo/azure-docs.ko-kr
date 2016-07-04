<properties
   pageTitle="MongoDB를 사용하여 Node.js 응용 프로그램 배포 | Microsoft Azure"
   description="여러 게스트 실행 파일을 패키지하여 Azure 서비스 패브릭 클러스터에 배포하는 방법에 대한 연습"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/20/2016"
   ms.author="bscholl;mikhegn"/>


# 여러 개의 게스트 실행 파일 배포

이 문서에서는 [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool)에서 제공하는 서비스 패브릭 패키징 도구의 Preview 버전을 사용하여 여러 게스트 실행 파일을 패키지하고 Azure 서비스 패브릭에 배포하는 방법을 설명합니다.

서비스 패브릭 패키지를 수동으로 빌드하는 방법은 [서비스 패브릭에 게스트 실행 파일 배포](service-fabric-deploy-existing-app.md) 방법을 참조하세요.

이 연습에서는 MongoDB를 데이터 저장소로 사용하는 Node.js 프런트 엔드를 통해 응용 프로그램을 배포하는 방법을 보여 줍니다. 이 단계는 다른 응용 프로그램에 종속된 모든 응용 프로그램에 적용할 수 있습니다.

## Node.js 응용 프로그램 패키지

이 문서에서는 서비스 패브릭 클러스터의 노드에 Node.js가 아직 설치되지 않은 것으로 가정합니다. 결과적으로 패키징 전에 노드 응용 프로그램의 루트 디렉터리에 node.exe를 추가해야 합니다. Node.js 응용 프로그램의 디렉터리 구조(Express 웹 프레임워크 및 Jade 템플릿 엔진 사용)는 다음과 비슷합니다.

```
|-- NodeApplication
	|-- bin
        |-- www
	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
	|-- public
        |-- images
        |-- etc.
	|-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

다음 단계는 Node.js 응용 프로그램에 대한 응용 프로그램 패키지를 만드는 것입니다. 아래 코드는 Node.js 응용 프로그램을 포함하는 서비스 패브릭 응용 프로그램 패키지를 만듭니다.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

다음은 사용 중인 매개 변수에 대한 설명입니다.

- **/source**: 패키지할 응용 프로그램의 디렉터리를 가리킵니다.
- **/target**: 패키지를 만들 디렉터리를 정의합니다. 이 디렉터리는 원본 디렉터리와 달라야 합니다.
- **/appname**: 기존 응용 프로그램의 응용 프로그램 이름을 정의합니다. 이 이름은 매니페스트에서 서비스 패브릭 응용 프로그램 이름이 아니라 서비스 이름으로 변환된다는 점을 이해하는 것이 중요합니다.
- **/exe**: 서비스 패브릭이 시작할 실행 파일을 정의합니다. 이 예에서는 `node.exe`입니다.
- **/ma**: 실행 파일을 시작하는 데 사용되는 인수를 정의합니다. Node.js가 설치되지 않았기 때문에 서비스 패브릭에서 `node.exe bin/www`를 실행하여 Node.js 웹 서버를 시작해야 합니다. `/ma:'bin/www'`는 `bin/ma`를 node.exe 인수로 사용하도록 패키징 도구에 지시합니다.
- **/AppType**: 서비스 패브릭 응용 프로그램 형식 이름을 정의합니다.

>[AZURE.NOTE] 또한 Visual Studio를 사용하여 응용 프로그램 프로젝트의 일환으로 응용 프로그램 패키지를 생성할 수 있습니다. Visual Studio 솔루션을 구축하는 Visual Studio 프로젝트에서 원본을 연결하려는 경우 응용 프로그램 패키지는 원본의 변경 내용으로 최신 상태로 업데이트됩니다. [Visual Studio를 사용하여 기존 응용 프로그램 패키징](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)

/target 매개 변수에서 지정한 디렉터리로 이동하면 다음과 같이 도구가 완벽하게 작동하는 서비스 패브릭 패키지를 만든 것을 볼 수 있습니다.

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
		      |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
		      |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
생성된 ServiceManifest.xml에는 이제 아래의 코드 조각과 같이 Node.js 웹 서버를 시작하는 방법을 설명하는 섹션이 있습니다.

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
이 샘플에서 Node.js 웹 서버는 포트 3000에서 수신하므로 ServiceManifest.xml 파일의 끝점 정보를 아래와 같이 업데이트해야 합니다.

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
Node.js 응용 프로그램을 패키지했으므로 이제 MongoDB를 패키지할 수 있습니다. 앞서 언급했듯이 이제부터 수행할 단계는 Node.js 및 MongoDB에만 적용되는 것이 아니라 하나의 서비스 패브릭 응용 프로그램으로 패키지되어야 하는 모든 응용 프로그램에 적용됩니다.

MongoDB를 패키지하려면 Mongod.exe 및 Mongo.exe를 패키지해야 합니다. 이 두 이진 파일은 MongoDB 설치 디렉터리의 `bin` 디렉터리에 있습니다. 디렉터리 구조는 아래와 유사합니다.

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
서비스 패브릭에서 아래와 유사한 명령을 사용하여 MongoDB를 시작해야 하므로 MongoDB를 패키지할 때 `/ma` 매개 변수를 사용해야 합니다.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] MongoDB 데이터 디렉터리를 노드의 로컬 디렉터리에 넣으면 노드 오류 발생 시 데이터가 보존되지 않습니다. 데이터 손실을 방지하려면 지속형 저장소를 사용하거나 MongoDB 복제본 세트를 구현해야 합니다.

PowerShell 또는 명령 셸에서 다음 매개 변수와 함께 패키징 도구를 실행합니다.

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

서비스 패브릭 응용 프로그램 패키지에 MongoDB를 추가하려면 /target 매개 변수가 응용 프로그램 매니페스트와 Node.js 응용 프로그램이 이미 포함된 디렉터리를 가리켜야 합니다. 또한 동일한 ApplicationType 이름을 사용해야 합니다.

>[AZURE.NOTE] 또한 Visual Studio를 사용하여 응용 프로그램 프로젝트의 일환으로 응용 프로그램 패키지를 생성할 수 있습니다. Visual Studio 솔루션을 구축하는 Visual Studio 프로젝트에서 원본을 연결하려는 경우 응용 프로그램 패키지는 원본의 변경 내용으로 최신 상태로 업데이트됩니다. [Visual Studio를 사용하여 기존 응용 프로그램 패키징](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)

해당 디렉터리로 이동하여 도구가 만든 항목을 확인합니다.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
		    |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
도구가 MongoDB 이진 파일이 포함된 디렉터리에 새 폴더 MongoDB를 추가했음을 확인할 수 있습니다. 이제 `ApplicationManifest.xml` 파일을 열면 패키지에 Node.js 응용 프로그램과 MongoDB가 모두 포함된 것을 볼 수 있습니다. 아래 코드는 응용 프로그램 매니페스트의 내용입니다.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

마지막 단계는 아래의 PowerShell 스크립트를 사용하여 로컬 서비스 패브릭 클러스터에 응용 프로그램을 게시하는 것입니다.

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

>[AZURE.NOTE] Visual Studio를 사용하면 디버깅(F5) 또는 게시 마법사를 사용하여 응용 프로그램을 로컬로 게시할 수 있습니다.

응용 프로그램을 성공적으로 로컬 클러스터에 게시한 후에는 Node.js 응용 프로그램의 서비스 매니페스트에 입력한 포트(예: http://localhost:3000)에서 Node.js 응용 프로그램에 액세스할 수 있습니다.

이 자습서에서는 간편하게 두 기존 응용 프로그램을 하나의 서버 패브릭 응용 프로그램으로 패키지하는 방법을 알아보았습니다. 또한 고가용성 및 상태 시스템 통합 같은 서비스 패브릭의 장점을 활용할 수 있도록 응용 프로그램을 서비스 패브릭에 배포하는 방법도 알아보았습니다.

## 다음 단계

- [게스트 응용 프로그램을 수동으로 패키지](service-fabric-deploy-existing-app.md)하는 방법을 알아보세요.

<!---HONumber=AcomDC_0622_2016-->