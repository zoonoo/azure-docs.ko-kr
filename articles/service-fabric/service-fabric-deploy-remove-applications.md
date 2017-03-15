---
title: "Service Fabric 응용 프로그램 배포 | Microsoft Docs"
description: "서비스 패브릭에서 응용 프로그램을 배포 및 제거하는 방법"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: dbd4dd3cadf162ea18d58639d31589f7b9b8efc3
ms.openlocfilehash: 2dfdcd08501a63d62ec6ba565d1abc7d42c8c680
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>PowerShell을 사용하여 응용 프로그램 배포 및 제거
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

일단 [응용 프로그램 형식이 패키지화되면][10] Azure Service Fabric 클러스터에 배포될 준비가 된 것입니다. 배포에는 다음 세 단계가 포함됩니다.

1. 이미지 저장소에 응용 프로그램 패키지 업로드
2. 응용 프로그램 형식 등록
3. 응용 프로그램 인스턴스 만들기

응용 프로그램을 배포하고 인스턴스가 클러스터에서 실행되면 응용 프로그램 인스턴스와 해당 응용 프로그램 유형을 삭제할 수 있습니다. 클러스터에서 응용 프로그램을 완전히 제거하려면 다음 단계를 수행합니다.

1. 실행 중인 응용 프로그램 인스턴스 제거(또는 삭제)
2. 더 이상 필요하지 않은 경우 응용 프로그램 유형 등록 취소
3. 이미지 저장소에서 응용 프로그램 패키지 제거

로컬 개발 클러스터에서 [Visual Studio를 사용하여 응용 프로그램을 개발 및 배포](service-fabric-publish-app-remote-cluster.md)하는 경우 이전의 모든 단계는 PowerShell 스크립트를 통해 자동으로 처리됩니다.  이 스크립트는 응용 프로그램 프로젝트의 *Scripts* 폴더에 있습니다. 이 문서에서는 Visual Studio 외부에서 동일한 작업을 수행할 수 있도록 스크립트에서 수행하는 작업에 대한 배경을 설명합니다. 
 
## <a name="connect-to-the-cluster"></a>클러스터에 연결
이 문서에서는 PowerShell 명령을 실행하기에 앞서 언제나 [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster)를 사용하여 Service Fabric 클러스터에 연결하는 것으로 시작합니다. 로컬 개발 클러스터에 연결하려면 다음을 실행합니다.

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Azure Active Directory, X509 인증서 또는 Windows Active Directory를 사용하여 보안된 원격 클러스터 또는 클러스터에 연결하는 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요.

## <a name="upload-the-application-package"></a>응용 프로그램 패키지 업로드
응용 프로그램 패키지를 업로드하면 내부 서비스 패브릭 구성 요소에 의해 액세스할 수 있는 위치에 배치됩니다. 앱 패키지를 로컬로 확인하려는 경우 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet을 사용합니다.  [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 명령으로 응용 프로그램 패키지를 클러스터 이미지 저장소에 업로드합니다. 서비스 패브릭 SDK PowerShell 모듈의 일부인 **Get ImageStoreConnectionStringFromClusterManifest** cmdlet는 이미지 저장소 연결 문자열을 가져오는 데 사용됩니다.  SDK 모듈을 가져오려면 다음을 실행합니다.

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Visual Studio에서 *MyApplication*이라는 응용 프로그램을 빌드하고 패키지한다고 가정해 보겠습니다. 기본적으로 ApplicationManifest.xml에 나열된 응용 프로그램 유형 이름은 "MyApplicationType"입니다.  필요한 응용 프로그램 매니페스트, 서비스 매니페스트 및 코드/구성/데이터 패키지가 포함된 응용 프로그램 패키지는 *C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*에 있습니다. 

다음 명령은 응용 프로그램 패키지의 내용을 나열합니다.

```powershell
PS C:\> tree /f 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

다음 예제에서는 패키지를 이미지 저장소의 "MyApplicationV1"이라는 폴더에 업로드합니다.

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

*-ApplicationPackagePathInImageStore* 매개 변수를 지정하지 않으면 응용 프로그램 패키지가 이미지 저장소의 "Debug" 폴더에 복사됩니다.

이미지 저장소 및 이미지 저장소 연결 문자열에 대한 보충 정보는 [이미지 저장소 연결 문자열 이해](service-fabric-image-store-connection-string.md)를 참조하세요.

## <a name="register-the-application-package"></a>응용 프로그램 패키지 등록
응용 프로그램 패키지가 등록되는 경우 응용 프로그램 매니페스트에서 선언된 응용 프로그램 형식과 버전을 사용할 수 있게 됩니다. 시스템은 이전 단계에서 업로드된 패키지를 읽고, 패키지를 확인하며, 처리된 패키지를 내부 시스템 위치에 복사합니다.  

[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet을 실행하여 클러스터에서 응용 프로그램 유형을 등록하고 배포할 수 있도록 합니다.

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1"은 응용 프로그램 패키지가 있는 이미지 저장소의 폴더입니다. 이름이 "MyApplicationType"이고 버전이 "1.0.0"인(둘 다 응용 프로그램 매니페스트에 있음) 응용 프로그램 유형이 이제 클러스터에 등록됩니다.

[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) 명령은 시스템에서 응용 프로그램 패키지를 성공적으로 등록한 후에만 반환합니다. 등록에 걸리는 시간은 응용 프로그램 패키지의 크기 및 콘텐츠에 따라 다릅니다. **-TimeoutSec** 매개 변수는 필요한 경우 더 긴 제한 시간을 제공합니다(기본 제한 시간은 60초).  큰 앱 패키지가 있고 제한 시간이 발생하는 경우 **-Async** 매개 변수를 사용합니다.

[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) 명령은 성공적으로 등록된 모든 응용 프로그램 유형 버전과 해당 등록 상태를 나열합니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>응용 프로그램 만들기
[New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) cmdlet을 사용하여 성공적으로 등록된 모든 응용 프로그램 유형 버전에서 응용 프로그램을 인스턴스화할 수 있습니다. 각 응용 프로그램의 이름은 반드시 *fabric:* 체계로 시작하고 각 응용 프로그램 인스턴스에 대해 고유해야 합니다. 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 모든 기본 서비스도 만들어집니다.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
등록된 응용 프로그램 형식의 주어진 어떤 버전에 대해서도 여러 개의 응용 프로그램 인스턴스를 만들 수 있습니다. 각 응용 프로그램 인스턴스는 자체 작업 디렉터리 및 프로세스와는 별도로 실행됩니다.

클러스터에서 실행 중인 명명된 응용 프로그램과 서비스를 확인하려면 [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 및 [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) cmdlet을 실행합니다.

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>응용 프로그램 제거
응용 프로그램 인스턴스가 더 이상 필요하지 않은 경우 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) cmdlet을 사용하여 해당 응용 프로그램 인스턴스를 이름별로 영구적으로 제거할 수 있습니다. [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication)은 응용 프로그램에 속한 모든 서비스를 자동으로 제거하고, 해당 서비스 상태를 모두 영구적으로 제거합니다. 이 작업은 되돌릴 수 없으며 응용 프로그램 상태는 복구할 수 없습니다.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>응용 프로그램 유형 등록 취소
응용 프로그램 유형의 특정 버전이 더 이상 필요하지 않은 경우 [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) cmdlet 사용하여 해당 응용 프로그램 유형을 등록 취소해야 합니다. 사용하지 않는 응용 프로그램 유형을 등록 취소하면 이미지 저장소에서 사용하는 저장 공간이 해제됩니다. 응용 프로그램 형식은 이에 대해 인스턴스화된 응용 프로그램이나 이를 참조하는 보류 중인 응용 프로그램이 없는 한 등록 취소할 수 있습니다.

[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype)을 실행하여 현재 클러스터에 등록된 응용 프로그램 유형을 확인합니다.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

[Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype)을 실행하여 특정 응용 프로그램 유형의 등록을 취소합니다.

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```
## <a name="remove-an-application-package-from-the-image-store"></a>이미지 저장소에서 응용 프로그램 패키지 제거
응용 프로그램 패키지가 더 이상 필요하지 않은 경우 이미지 저장소에서 해당 응용 프로그램 패키지를 삭제하여 시스템 리소스를 확보할 수 있습니다.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>문제 해결
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage가 ImageStoreConnectionString을 요청함
서비스 패브릭 SDK 환경은 이미 올바른 기본 설정값을 가지고 있습니다. 하지만 필요한 경우 모든 명령에 대한 ImageStoreConnectionString은 서비스 패브릭 클러스터가 사용 중인 값과 일치해야 합니다. [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) 명령을 사용하여 검색된 클러스터 매니페스트에서 ImageStoreConnectionString을 찾을 수 있습니다.

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

ImageStoreConnectionString은 클러스터 매니페스트에 있습니다.

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

## <a name="next-steps"></a>다음 단계
[서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

[서비스 패브릭 상태 소개](service-fabric-health-introduction.md)

[서비스 패브릭 서비스 진단 및 문제 해결](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[서비스 패브릭에서 응용 프로그램 모델링](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

