<properties
   pageTitle="서비스 패브릭 응용 프로그램 배포 | Microsoft Azure"
   description="서비스 패브릭에서 응용 프로그램을 배포 및 제거하는 방법"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/03/2016"
   ms.author="ryanwi"/>

# 응용 프로그램 배포

일단 [응용 프로그램 형식이 패키지화되면][10] Azure 서비스 패브릭 클러스터에 배포될 준비가 된 것입니다. 배포에는 다음 세 단계가 포함됩니다.

1. 응용 프로그램 패키지 업로드
2. 응용 프로그램 형식 등록
3. 응용 프로그램 인스턴스 생성

>[AZURE.NOTE] 로컬 개발 클러스터에서 개발 및 배포에 Visual Studio를 사용하는 경우 아래에 설명한 모든 단계는 응용 프로그램 프로젝트의 스크립트 폴더에 있는 PowerShell 스크립트를 통해 자동으로 처리됩니다. 이 문서에서는 해당 스크립트가 하는 일과, 그로 인해 Visual Studio 외부에서 동일한 작업을 수행할 수 있는 배경을 설명합니다.

## 응용 프로그램 패키지 업로드

응용 프로그램 패키지를 업로드하면 내부 서비스 패브릭 구성 요소에 의해 액세스할 수 있는 위치에 배치됩니다. PowerShell을 사용하여 업로드를 수행할 수 있습니다. 이 문서에서는 PowerShell 명령을 실행하기에 앞서 언제나 **Connect-ServiceFabricCluster**를 사용하여 서비스 패브릭 클러스터에 연결하는 것으로 시작합니다.

필요한 응용 프로그램 매니페스트, 서비스 매니페스트, 코드/구성/데이터 패키지가 들어 있는 *MyApplicationType*이라는 폴더가 있다고 가정합니다. **Copy-ServiceFabricApplicationPackage** 명령으로 패키지를 업로드합니다. 예:

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## 응용 프로그램 패키지 등록

응용 프로그램 패키지 등록은 응용 프로그램 매니페스트에서 사용하기 위해 응용 프로그램 형식과 버전을 사용할 수 있도록 합니다. 시스템은 이전 단계에서 업로드된 패키지를 읽고, 패키지를 확인하며(로컬에서 실행 중인 **Test-ServiceFabricApplicationPackage**와 동일한지), 패키지 콘텐츠를 처리하고, 처리된 패키지를 내부 시스템 위치에 복사합니다.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

**Register-ServiceFabricApplicationType** 명령은 시스템이 응용 프로그램 패키지를 성공적으로 복사한 후에만 반환합니다. 여기에 걸리는 시간은 응용 프로그램 패키지의 콘텐츠에 따라 다릅니다. **-TimeoutSec** 매개 변수는 필요한 경우 더 긴 제한 시간을 제공합니다. (기본 제한 시간은 60초입니다.)

**Get-ServiceFabricApplicationType** 명령은 성공적으로 등록된 모든 응용 프로그램 형식 버전을 나열합니다.

## 응용 프로그램 만들기

**New-ServiceFabricApplication** 명령을 사용하여 성공적으로 등록된 모든 응용 프로그램 형식 버전을 사용하여 응용 프로그램을 인스턴스화할 수 있습니다. 각 응용 프로그램의 이름은 반드시 *fabric:* 체계로 시작하고 각 응용 프로그램 인스턴스에 대해 고유해야 합니다. 기본 서비스가 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 경우 이때 이러한 서비스도 생성됩니다.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

**Get-ServiceFabricApplication** 명령은 해당 모든 상태에 따라 성공적으로 만들어진 모든 응용 프로그램 인스턴스를 나열합니다.

**Get-ServiceFabricService** 명령은 주어진 응용 프로그램 인스턴스 내에서 성공적으로 만들어진 모든 서비스 인스턴스를 나열합니다. 기본 서비스(있는 경우)는 여기에 나열됩니다.

등록된 응용 프로그램 형식의 주어진 어떤 버전에 대해서도 여러 개의 응용 프로그램 인스턴스를 만들 수 있습니다. 각 응용 프로그램 인스턴스는 자체 작업 디렉터리 및 프로세스와는 별도로 실행됩니다.

## 응용 프로그램 제거

응용 프로그램 인스턴스가 더 이상 필요하지 않은 경우 **Remove-ServiceFabricApplication** 명령을 사용하여 영구적으로 제거할 수 있습니다. 이 명령은 모든 서비스 상태를 영구적으로 제거하고 해당 응용 프로그램에 속한 모든 서비스를 자동으로 제거합니다. 이 작업은 되돌릴 수 없으며 응용 프로그램 상태는 복구할 수 없습니다.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

응용 프로그램 형식의 특정 버전이 더 이상 필요하지 않은 경우 **Unregister-ServiceFabricApplicationType** 명령을 사용하여 등록 취소할 수 있습니다. 사용하지 않는 형식을 등록 취소하면 이미지 저장소에서 해당 형식의 응용 프로그램 패키지 콘텐츠에 사용되는 저장소 공간을 비웁니다. 응용 프로그램 형식은 이에 대해 인스턴스화된 응용 프로그램이나 이를 참조하는 보류 중인 응용 프로그램이 없는 한 등록 취소할 수 있습니다.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## 문제 해결

### Copy-ServiceFabricApplicationPackage가 ImageStoreConnectionString을 요청함

서비스 패브릭 SDK 환경은 이미 올바른 기본 설정값을 가지고 있습니다. 하지만 필요한 경우 모든 명령에 대한 ImageStoreConnectionString은 서비스 패브릭 클러스터가 사용 중인 값과 일치해야 합니다. **Get-ServiceFabricClusterManifest** 명령을 통해 검색된 클러스터 매니페스트에서 이 값을 찾을 수 있습니다.

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## 다음 단계

[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

[서비스 패브릭 상태 소개](service-fabric-health-introduction.md)

[서비스 패브릭 서비스 진단 및 문제 해결](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭에서 응용 프로그램 모델링](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_0608_2016-->