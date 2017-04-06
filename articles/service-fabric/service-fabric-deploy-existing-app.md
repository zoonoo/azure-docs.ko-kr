---
title: "Azure Service Fabric에 기존 실행 파일 배포 | Microsoft Docs"
description: "Service Fabric 클러스터에 배포할 수 있도록 기존 응용 프로그램을 게스트 실행 파일로 패키징하는 방법에 대한 연습"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/17/2016
ms.author: mfussell;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e9c53dc601406961ee7aeca2e350ba14e691cb9b
ms.lasthandoff: 03/29/2017


---
# <a name="deploy-a-guest-executable-to-service-fabric"></a>서비스 패브릭에 게스트 실행 파일 배포
Azure 서비스 패브릭에서 Node.js, Java 또는 네이티브 응용 프로그램과 같은 모든 유형의 응용 프로그램을 실행할 수 있습니다. Service Fabric에서는 이러한 유형의 응용 프로그램을 게스트 실행 파일이라고 합니다.
게스트 실행 파일은 서비스 패브릭에서 상태 비저장 서비스처럼 취급됩니다. 결과적으로 가용성 및 기타 메트릭을 기반으로 클러스터의 노드에 배치됩니다. 이 문서에서는 Visual Studio 또는 명령줄 유틸리티를 사용하여 Service Fabric 클러스터에 게스트 실행 파일을 패키징 및 배포하는 방법을 설명합니다.

이 문서에서는 게스트 실행 파일을 패키징하고 Service Fabric에 배포하는 단계를 다룹니다.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>서비스 패브릭에서 게스트 실행 파일을 실행할 때의 이점
Service Fabric 클러스터에서 게스트 실행 파일을 실행하면 다음과 같은 몇 가지 장점이 있습니다.

* 고가용성. Service Fabric에서 실행되는 응용 프로그램은 고가용성 모드입니다. Service Fabric은 응용 프로그램 인스턴스가 실행 중인지 확인합니다.
* 상태 모니터링. Service Fabric 상태 모니터링은 응용 프로그램이 실행 중인지 감지하고 오류가 있으면 진단 정보를 제공합니다.   
* 응용 프로그램 수명 주기 관리. Service Fabric은 가동 중지 시간 없이 업그레이드를 제공할 뿐 아니라 업그레이드 중에 나쁜 상태 이벤트가 보고되면 이전 버전으로 자동 롤백을 제공합니다.    
* 밀도. 한 클러스터에서 여러 응용 프로그램을 실행할 수 있으므로 응용 프로그램을 고유의 하드웨어에서 실행할 필요가 없습니다.

## <a name="samples"></a>샘플
* [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>응용 프로그램 및 서비스 매니페스트 파일 개요
게스트 실행 파일을 배포하는 일환으로 [응용 프로그램 모델](service-fabric-application-model.md)에 설명된 Service Fabric 패키징 및 배포 모델을 이해하는 것이 유용합니다. Service Fabric 패키징 모델은 두 XML 파일(응용 프로그램 및 서비스 매니페스트)에 의존합니다. ApplicationManifest.xml 및 ServiceManifest.xml에 대한 스키마 정의는 Service Fabric SDK와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다.

* **응용 프로그램 매니페스트** 응용 프로그램 매니페스트는 응용 프로그램을 설명하는 데 사용되며 응용 프로그램을 구성하는 서비스와 하나 이상의 서비스 배포 방법(예: 인스턴스 수)을 정의하는 데 사용되는 기타 매개 변수를 나열합니다.
  
  Service Fabric에서 응용 프로그램은 배포 및 업그레이드 단위입니다. 응용 프로그램은 잠재적인 오류 및 잠재적 롤백이 관리되는 하나의 단위로 업그레이드될 수 있습니다. Service Fabric은 업그레이드 프로세스의 성공을 보장하며, 업그레이드가 실패할 경우 응용 프로그램을 알 수 없거나 불안정한 상태로 남겨 두지 않습니다.
* **서비스 매니페스트** 서비스 매니페스트는 서비스의 구성 요소를 설명합니다. 서비스의 이름 및 유형과 같은 데이터와 그에 대한 코드 및 구성을 포함합니다. 또한 서비스 매니페스트는 서비스가 배포되면 서비스를 구성하는 데 사용할 수 있는 몇 가지 추가 매개 변수도 포함하고 있습니다.

## <a name="application-package-file-structure"></a>응용 프로그램 패키지 파일 구조
응용 프로그램을 Service Fabric에 배포하려면 응용 프로그램은 미리 정의된 디렉터리 구조를 따라야 합니다. 다음은 해당 구조의 예입니다.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot는 응용 프로그램을 정의하는 ApplicationManifest.xml 파일을 포함합니다. 응용 프로그램에 포함된 각 서비스에 대한 하위 디렉터리 서비스는 서비스가 필요한 모든 아티팩트를 포함하는 데 사용됩니다. 이러한 하위 디렉터리는 ServiceManifest.xml이며 일반적으로 다음과 같습니다.

* *Code*. 이 디렉터리는 서비스 코드를 포함합니다.
* *Config*. 이 디렉터리는 런타임에 서비스가 액세스하여 특정 구성 설정을 검색할 수 있는 settings.xml 파일(필요한 경우 다른 파일도 포함)을 포함합니다.
* *Data*. 서비스에 필요할 수도 있는 추가 로컬 데이터를 저장하는 추가 디렉터리입니다. Data는 사용 후 삭제되는 데이터를 저장하는 용도로만 사용해야 합니다. 예를 들어 장애 조치 중에 서비스를 다시 배치해야 하는 경우 Service Fabric은 변경 내용을 데이터 디렉터리에 복사 또는 복제하지 않습니다.

> [!NOTE]
> `config` 및 `data` 디렉터리가 필요 없으면 만들지 않아도 됩니다.
> 
> 

## <a name="package-an-existing-executable"></a>기존 실행 파일 패키징
게스트 실행 파일을 패키징할 경우 Visual Studio 프로젝트 템플릿을 사용하거나 [응용 프로그램 패키지를 수동으로 만들도록](#manually) 선택할 수 있습니다. Visual Studio를 사용하면 새 프로젝트 템플릿에 의해 응용 프로그램 패키지 구조 및 매니페스트 파일이 생성됩니다.

> [!TIP]
> 기존 Windows 실행 파일을 서비스로 패키징하는 가장 쉬운 방법은 Visual Studio를 사용하는 것입니다.
> 
> 

## <a name="use-visual-studio-to-package-an-existing-executable"></a>Visual Studio를 사용하여 기존 실행 파일 패키징
Visual Studio는 게스트 실행 파일을 서비스 패브릭 클러스터에 배포할 수 있도록 서비스 패브릭 서비스 템플릿을 제공합니다.

1. **파일** > **새 프로젝트**를 선택하여 Service Fabric 응용 프로그램을 만듭니다.
2. **게스트 실행 파일**을 서비스 템플릿으로 선택합니다.
3. **찾아보기**를 클릭하여 실행 파일이 포함된 폴더를 선택하고 매개 변수의 나머지를 입력하여 서비스를 만듭니다.
   * *코드 패키지 동작*. 폴더의 모든 콘텐츠를 Visual Studio 프로젝트에 복사하도록 설정할 수 있으며 이것은 실행 파일이 변경되지 않는 경우에 유용합니다. 실행 파일을 변경하고 동적으로 새 빌드를 선택할 수 있는 기능을 원하는 경우 대신 폴더에 연결하도록 선택할 수 있습니다. Visual Studio에서 응용 프로그램 프로젝트를 만들 경우 연결된 폴더를 사용할 수 있습니다. 이는 프로젝트 내에서 원본 위치에 연결되면 원본 대상에서 게스트 실행 파일을 업데이트할 수 있습니다. 이 업데이트는 빌드의 응용 프로그램 패키지의 일부가 됩니다.
   * *프로그램*은 서비스를 시작하기 위해 실행해야 하는 실행 파일을 지정합니다.
   * *인수*는 실행 파일에 전달되어야 하는 인수를 지정합니다. 인수가 있는 매개 변수 목록이 될 수도 있습니다.
   * *WorkingFolder*는 시작될 프로세스에 대한 작업 디렉터리를 지정합니다. 세 가지 값을 지정할 수 있습니다.
     * `CodeBase`는 작업 디렉터리가 응용 프로그램 패키지의 코드 디렉터리에 설정되도록 지정합니다(이전 파일 구조에 표시된 `Code` 디렉터리).
     * `CodePackage`는 작업 디렉터리가 응용 프로그램 패키지의 루트에 설정되도록 지정합니다(이전 파일 구조에 표시된 `GuestService1Pkg`).
     * `Work`는 파일이 work라는 하위 디렉터리에 배치되도록 지정합니다.
4. 서비스에 이름을 지정하고 **확인**을 클릭합니다.
5. 서비스에서 통신에 끝점이 필요한 경우 이제 프로토콜, 포트, 형식을 ServiceManifest.xml 파일에 추가할 수 있습니다. 예: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
6. 이제 패키지를 사용하고 Visual Studio에서 솔루션을 디버깅하여 로컬 클러스터에 대해 작업을 게시할 수 있습니다. 준비가 되면 원격 클러스터로 응용 프로그램을 게시하거나 원본 제어에 대한 솔루션을 체크 인합니다.
7. Service Fabric Explorer에서 실행 중인 게스트 실행 파일 서비스를 보는 방법을 보려면 이 문서의 끝으로 이동합니다.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>기존 실행 파일을 수동으로 패키징하고 배포하기
게스트 실행 파일을 수동으로 패키징하는 과정은 다음과 같은 일반 단계를 기반으로 합니다.

1. 패키지 디렉터리 구조를 만듭니다.
2. 응용 프로그램의 코드 및 구성 파일을 추가합니다.
3. 서비스 매니페스트 파일을 편집합니다.
4. 응용 프로그램 매니페스트 파일을 편집합니다.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>패키지 디렉터리 구조 만들기
이전 섹션에서 "응용 프로그램 패키지 파일 구조"에 설명된 대로 디렉터리 구조를 만들어서 시작할 수 있습니다.

### <a name="add-the-applications-code-and-configuration-files"></a>응용 프로그램의 코드 및 구성 파일 추가
디렉터리 구조를 만든 후에 응용 프로그램의 코드 및 구성 파일을 코드 및 구성 디렉터리 아래에 추가할 수 있습니다. 코드 또는 구성 디렉터리 아래에 하위 디렉터리 또는 추가 디렉터리를 만들 수도 있습니다.

Service Fabric은 응용 프로그램 루트 디렉터리의 내용에 대한 `xcopy`를 수행하므로 두 상위 디렉터리인 code 및 settings를 만들지 않고도 사용할 수 있는 미리 정의된 구조가 없습니다. 하지만 원한다면 다른 이름을 선택할 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

> [!NOTE]
> 응용 프로그램이 필요한 모든 파일 및 종속성을 포함했는지 확인합니다. Service Fabric은 응용 프로그램의 서비스를 배포할 클러스터의 모든 노드에서 응용 프로그램 패키지의 콘텐츠를 복사합니다. 패키지에는 응용 프로그램 실행에 필요한 모든 코드가 있어야 합니다. 종속성이 이미 설치되어 있다고 가정하지 마세요.
> 
> 

### <a name="edit-the-service-manifest-file"></a>서비스 매니페스트 파일 편집
다음 정보를 포함하도록 서비스 매니페스트 파일을 편집하는 것이 다음 단계입니다.

* 서비스 형식 이름 서비스 패브릭이 서비스를 식별하기 위해 사용하는 ID입니다.
* 응용 프로그램을 시작하기 위해 사용하는 명령(ExeHost).
* 응용 프로그램을 설치하기 위해 실행해야 하는 모든 스크립트(SetupEntryPoint)

다음은 `ServiceManifest.xml` 파일의 예제입니다.

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

다음 섹션에서 업데이트해야 하는 파일의 다른 부분을 살펴보겠습니다.

#### <a name="update-servicetypes"></a>ServiceTypes 업데이트
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* `ServiceTypeName`의 이름을 자유롭게 선택할 수 있습니다. 이 값은 `ApplicationManifest.xml` 파일에서 서비스를 식별하는 데 사용됩니다.
* `UseImplicitHost="true"`를 지정합니다. 이 특성은 서비스가 자체 포함된 앱을 기반으로 하므로 모든 서비스 패브릭이 응용 프로그램을 프로세스로 실행하여 상태를 모니터링해야 한다는 사실을 서비스 패브릭에 전달합니다.

#### <a name="update-codepackage"></a>CodePackage 업데이트
CodePackage 요소는 서비스 코드의 위치(및 버전)을 지정합니다.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 요소는 서비스 코드가 들어 있는 응용 프로그램 패키지의 디렉터리 이름을 지정하는 데 사용됩니다. `CodePackage`에도 `version` 특성이 있습니다. 이 요소는 코드 버전을 지정하는 데 사용할 수 있으며, Service Fabric에서 응용 프로그램 수명 주기 관리 인프라를 사용하여 서비스 코드를 업그레이드하는 데 사용할 수도 있습니다.

#### <a name="optional-update-setupentrypoint"></a>선택 사항: SetupEntryPoint 업데이트
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint 요소는 서비스의 코드를 시작하기 전에 실행되어야 하는 실행 파일 또는 배치 파일을 지정하는 데 사용됩니다. 선택적 단계이므로 초기화가 필수가 아닌 경우에는 포함되지 않아도 됩니다. SetupEntrypoint는 서비스를 다시 시작할 때마다 실행됩니다.

SetupEntryPoint가 하나밖에 없으므로 응용 프로그램의 설치에 여러 스크립트가 필요한 경우 설치 스크립트를 배치 파일 하나에 그룹화되어야 합니다. SetupEntryPoint는 실행 파일, 배치 파일, PowerShell cmdlet 등 각종 파일을 실행할 수 있습니다. 자세한 내용은 [SetupEntryPoint 구성](service-fabric-application-runas-security.md)을 참조하세요.

앞의 예제에서 SetupEntryPoint는 코드 디렉터리의 `scripts` 하위 디렉터리에 있는 `LaunchConfig.cmd`라는 배치 파일을 실행합니다(WorkingFolder 요소가 CodeBase로 설정되어 있다고 가정).

#### <a name="update-entrypoint"></a>EntryPoint 업데이트
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

서비스 매니페스트 파일의 `EntryPoint` 요소는 서비스를 시작하는 방법을 지정하는 데 사용됩니다. `ExeHost` 요소는 서비스를 시작하는 데 사용되어야 하는 실행 파일(및 인수)을 지정합니다.

* `Program`은 서비스를 시작해야 하는 실행 파일의 이름을 지정합니다.
* `Arguments` 는 실행 파일에 전달되어야 하는 인수를 지정합니다. 인수가 있는 매개 변수 목록이 될 수도 있습니다.
* `WorkingFolder`는 곧 시작될 프로세스의 작업 디렉터리를 지정합니다. 세 가지 값을 지정할 수 있습니다.
  * `CodeBase`는 작업 디렉터리가 응용 프로그램 패키지의 코드 디렉터리에 설정되도록 지정합니다(이전 파일 구조의 `Code` 디렉터리).
  * `CodePackage`는 작업 디렉터리가 응용 프로그램 패키지의 루트에 설정되도록 지정합니다(이전 파일 구조의 `GuestService1Pkg`).
    * `Work`는 파일이 work라는 하위 디렉터리에 배치되도록 지정합니다.

WorkingFolder는 응용 프로그램 또는 초기화 스크립트에서 상대 경로를 사용할 수 있도록 올바른 작업 디렉터리를 설정하는 데 유용합니다.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>통신을 위해 끝점을 업데이트하고 명명 서비스에 등록
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
앞의 예제에서 `Endpoint` 요소는 응용 프로그램에서 수신 대기할 수 있는 끝점을 지정합니다. 이 예제에서 Node.js 응용 프로그램은 포트 3000의 http에 수신 대기합니다.

또한 다른 서비스가 이 서비스에 대한 끝점 주소를 검색할 수 있도록 Service Fabric에 이 끝점을 명명 서비스에 게시하도록 요청할 수 있습니다. 이렇게 하면 게스트 실행 파일인 서비스 간에 통신을 할 수 있습니다.
게시된 끝점 주소는 `UriScheme://IPAddressOrFQDN:Port/PathSuffix`형식입니다. `UriScheme` 및 `PathSuffix`는 선택적 특성입니다. `IPAddressOrFQDN`은 이 실행 파일이 배치되고 계산되는 노드의 IP 주소 또는 정규화된 도메인 이름입니다.

다음 예제에서 서비스가 배포되면, 서비스 인스턴스에 대해 게시된 `http://10.1.4.92:3000/myapp/`와 유사한 끝점이 Service Fabric Explorer에 표시됩니다. 또는 로컬 컴퓨터인 경우, `http://localhost:3000/myapp/`가 표시됩니다.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
이 주소를 [역방향 프록시](service-fabric-reverseproxy.md)와 사용하여 서비스 간에 통신할 수 있습니다.

### <a name="edit-the-application-manifest-file"></a>응용 프로그램 매니페스트 파일 편집
`Servicemanifest.xml` 파일을 구성했으면 `ApplicationManifest.xml` 파일을 변경하여 올바른 서비스 유형 및 이름이 사용되는지 확인해야 합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
`ServiceManifestImport` 요소에서 앱에 포함할 서비스를 하나 이상 지정할 수 있습니다. 서비스는 `ServiceManifest.xml` 파일이 있는 디렉터리의 이름을 지정하는 `ServiceManifestName`으로 참조됩니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>로깅 설정
게스트 실행 파일의 경우 응용 프로그램 및 구성 스크립트가 오류를 표시할 때 콘솔 로그를 볼 수 있으므로 유용합니다.
콘솔 리디렉션은 `ConsoleRedirection` 요소를 사용하여 `ServiceManifest.xml` 파일에 구성할 수 있습니다.

> [!WARNING]
> 절대 프로덕션에 배포된 응용 프로그램의 콘솔 리디렉션 정책은 사용하지 마세요. 응용 프로그램 장애 조치(failover)에 영향을 줄 수 있기 때문입니다. 로컬 개발 및 디버깅 목적으로*만* 사용하세요.  
> 
> 

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

`ConsoleRedirection`은 콘솔 출력(stdout 및 stderr)을 작업 디렉터리에 리디렉션하는 데 사용할 수 있습니다. 이는 Service Fabric 클러스터에서 응용 프로그램을 설치하거나 실행하는 동안 오류가 없는지를 확인하는 기능을 제공합니다.

`FileRetentionCount` 는 작업 디렉터리에 저장되는 파일의 수를 결정합니다. 예를 들어 값이 5이면 이전 5개 실행에 대한 로그 파일이 작업 디렉터리에 저장되었다는 뜻입니다.

`FileMaxSizeInKb`는 로그 파일의 최대 크기를 지정합니다.

로그 파일은 서비스의 작업 디렉터리 중 하나에 저장됩니다. 파일이 어디에 있는지 확인하려면 Service Fabric Explorer를 사용하여 서비스가 실행 중인 노드와 현재 사용 중인 작업 디렉터리를 확인해야 합니다. 이 프로세스는 이 문서의 뒷부분에서 다루겠습니다.

## <a name="deployment"></a>배포
마지막 단계는 [응용 프로그램을 배포](service-fabric-deploy-remove-applications.md)하는 것입니다. 다음 PowerShell 스크립트는 로컬 개발 클러스터에 응용 프로그램을 배포하고 새 Service Fabric 서비스를 시작하는 방법을 보여 줍니다.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> 패키지가 크거나 파일이 많은 경우 이미지 저장소에 복사하기 전에 [패키지를 압축](service-fabric-package-apps.md#compress-a-package)합니다. [여기](service-fabric-deploy-remove-applications.md#upload-the-application-package)서 자세히 알아보세요.
>

서비스 패브릭 서비스를 다양한 "구성"으로 배포할 수 있습니다. 예를 들어 단일 또는 다중 인스턴스로 배포할 수도 있고, Service Fabric 클러스터의 각 노드에 서비스 인스턴스가 하나씩 있는 방식으로 배포할 수도 있습니다.

`New-ServiceFabricService` cmdlet의 `InstanceCount` 매개 변수는 서비스 패브릭 클러스터에서 실행되어야 하는 서비스의 인스턴스 개수를 지정하는 데 사용됩니다. 배포하는 응용 프로그램의 유형에 따라 `InstanceCount` 값을 설정할 수 있습니다. 가장 일반적인 두 가지 시나리오는 다음과 같습니다.

* `InstanceCount = "1"`. 이 경우 단 한 개의 서비스 인스턴스가 클러스터에 배포됩니다. 서비스 패브릭의 스케줄러는 서비스를 배포할 노드를 결정합니다.
* `InstanceCount ="-1"`. 이 경우 서비스의 단일 인스턴스가 Service Fabric 클러스터의 모든 노드에 배포됩니다. 그 결과 클러스터의 각 노드에 대한 서비스의 인스턴스를 하나(및 하나만) 갖게 됩니다.

이 구성은 프런트 엔드 응용 프로그램(예: REST 끝점)에 유용합니다. 클라이언트 응용 프로그램은 끝점을 사용하려면 클러스터의 노드에 "연결"해야 하기 때문입니다. 예를 들어, Service Fabric 클러스터의 모든 노드가 부하 분산 장치에 연결된 경우 이 구성을 사용할 수도 있습니다. 그러면 클라이언트 트래픽을 클러스터의 모든 노드에서 실행되는 서비스에 배포할 수 있습니다.

## <a name="check-your-running-application"></a>실행 중인 응용 프로그램 확인
서비스 패브릭 탐색기에서 서비스가 실행되고 있는 노드를 식별합니다. 이 예제에서는 Node1에서 실행됩니다.

![서비스가 실행 중인 노드](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

노드로 이동하여 응용 프로그램을 찾으면 디스크 상의 위치를 포함하여 필수 노드 정보가 표시됩니다.

![디스크 상의 위치](./media/service-fabric-deploy-existing-app/locationondisk2.png)

서버 탐색기를 사용하여 디렉터리를 찾아보면 다음 스크린샷과 같이 작업 디렉터리 및 서비스의 로그 폴더를 찾을 수 있습니다.

![로그 위치](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="creating-a-guest-executable-using-yeoman-for-service-fabric-on-linux"></a>Linux에서 Service Fabric용 Yeoman를 사용하여 게스트 실행 파일 만들기

Linux에서 실행되는 게스트를 만들고 배포하는 절차는 csharp 또는 java 응용 프로그램 배포와 동일합니다. 

1. 터미널에서 `yo azuresfguest`을 입력합니다.
2. 응용 프로그램의 이름을 지정합니다.
3. 첫 번째 서비스의 형식을 선택하고 이름을 지정합니다. 게스트 실행 파일에 대한 **게스트 이진**(및 컨테이너에 대한 **게스트 컨테이너**)를 선택하고, 호출되어야 하는 매개 변수와 실행 파일의 경로를 포함한 세부 정보를 제공합니다.

Yeoman는 설치 및 제거 스크립트와 함께 해당 응용 프로그램과 매니페스트 파일로 응용 프로그램 패키지를 작성합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 게스트 실행 파일을 패키징하고 서비스 패브릭에 배포하는 방법을 배웠습니다. 관련 정보 및 작업에 대해 다음 문서를 참조하세요.

* 패키징 도구 시험판의 링크를 포함하여 [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)
* [여러 개의 게스트 실행 파일 배포](service-fabric-deploy-multiple-apps.md)
* [Visual Studio를 사용하여 처음으로 서비스 패브릭 응용 프로그램 만들기](service-fabric-create-your-first-application-in-visual-studio.md)


