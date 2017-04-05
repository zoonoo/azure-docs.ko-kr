---
title: "Azure Service Fabric 앱 패키징 | Microsoft Docs"
description: "클러스터에 배포하기 전에 Service Fabric 응용 프로그램을 패키지하는 방법입니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/24/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 45bf19b4c8406cfc09624bef2b9c0f1c443d8fd6
ms.lasthandoff: 03/27/2017


---
# <a name="package-an-application"></a>응용 프로그램 패키지 작성
이 문서에서는 Service Fabric 응용 프로그램을 패키지하고 배포를 준비하는 방법에 대해 설명합니다.

## <a name="package-layout"></a>패키지 레이아웃
서비스 패브릭 클러스터에 배포할 수 있도록 응용 프로그램 매니페스트, 서비스 매니페스트 및 기타 필요한 패키지 파일을 특정 레이아웃으로 구성해야 합니다. 이 문서에 예로 제공된 매니페스트를 다음 디렉터리 구조로 구성해야 합니다.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

폴더는 각 해당 요소의 **Name** 특성과 일치하도록 이름이 지정됩니다. 예를 들어 서비스 매니페스트에 이름이 각각 **MyCodeA**와 **MyCodeB**인 코드 패키지가 두 개 있으면 이와 동일한 이름의 폴더 두 개에 각 코드 패키지에 필요한 바이너리가 포함됩니다.

## <a name="use-setupentrypoint"></a>SetupEntryPoint 사용
**SetupEntryPoint** 를 사용하는 일반적인 시나리오는 서비스를 시작하기 전에 실행 파일을 실행해야 하는 경우 또는 높은 권한을 사용하여 작업을 수행해야 하는 경우입니다. 예:

* 서비스 실행 파일에 필요한 환경 변수를 설정하고 초기화합니다. 이것은 서비스 패브릭 프로그래밍 모델을 통해 작성된 실행 파일에만 국한되지는 않습니다. 예를 들어 npm.exe 파일에는 node.js 응용 프로그램 배포를 위해 구성되는 환경 변수가 필요합니다.
* 보안 인증서를 설치하여 액세스 제어를 설정합니다.

**SetupEntryPoint**를 구성하는 방법에 대한 자세한 내용은 [서비스 설치 진입점에 대한 정책 구성](service-fabric-application-runas-security.md)을 참조하세요.  

## <a name="configure"></a>구성 
### <a name="build-a-package-by-using-visual-studio"></a>Visual Studio를 사용하여 패키지 빌드
Visual Studio 2015를 사용하여 응용 프로그램을 만드는 경우 패키지 명령을 사용하여 위에서 설명한 레이아웃과 일치하는 패키지를 자동으로 만들 수 있습니다.

패키지를 만들려면 솔루션 탐색기에서 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 아래와 같이 패키지 명령을 선택합니다.

![Visual Studio를 통해 응용 프로그램 패키징][vs-package-command]

패키징이 완료되면 **출력** 창에서 패키지의 위치를 찾습니다. Visual Studio에서 응용 프로그램을 배포 또는 디버깅할 때 패키징 단계가 자동으로 발생합니다.

### <a name="build-a-package-by-command-line"></a>명령줄로 패키지 빌드
`msbuild.exe`를 사용하여 응용 프로그램을 프로그래밍 방식으로 패키징할 수도 있습니다. 내부적으로 보면 Visual Studio가 실행하고 있으므로 출력은 동일합니다.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>패키지 테스트
[Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) 명령을 사용하여 PowerShell을 통해 로컬에서 패키지 구조를 확인할 수 있습니다.
이 명령은 매니페스트 구문 해석 문제를 확인하고 모든 참조의 유효성을 검사합니다. 이 명령은 패키지에 포함된 디렉터리와 파일의 구조적 정확성만 검사합니다.
필요한 파일이 모두 있는지 확인한 후에 코드 또는 데이터 패키지 내용을 검사하지 않습니다.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

이 오류는 서비스 매니페스트 *SetupEntryPoint* 에서 참조되는 **MySetup.bat** 파일이 코드 패키지에 없다는 뜻입니다. 누락된 파일이 추가되면, 응용 프로그램 검사가 통과됩니다.

```
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

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

응용 프로그램에 [응용 프로그램 매개 변수](service-fabric-manage-multiple-environment-app-configuration.md)가 정의되어 있으면 적절한 유효성 검사를 위해 [Test-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage)에 이 매개 변수를 전달할 수 있습니다.

응용 프로그램을 배포할 클러스터를 알고 있는 경우 이미지 저장소 연결 문자열에 전달하는 것이 좋습니다. 이 경우 이미 클러스터에서 실행 중인 이전 버전의 응용 프로그램에 대해서도 패키지의 유효성이 검사됩니다. 예를 들어 유효성 검사를 통해 동일한 버전이지만 다른 내용이 포함된 패키지를 이미 배포했는지 여부를 확인할 수 있습니다.  

응용 프로그램이 올바르게 패키지되고 유효성 검사를 통과하는 경우 압축이 필요하면 파일의 크기와 수를 기반으로 평가합니다. 

## <a name="compress-a-package"></a>패키지 압축
패키지가 크거나 파일이 많은 경우 이를 압축하여 더 빠르게 배포할 수 있습니다. 압축은 파일 수와 패키지 크기를 줄입니다.
[응용 프로그램 패키지를 업로드](service-fabric-deploy-remove-applications.md#upload-the-application-package)하는 경우 압축되지 않은 패키지를 업로드하는 것보다 시간이 더 오래 걸릴 수 있지만, 응용 프로그램 유형을 더 빠르게 [등록](service-fabric-deploy-remove-applications.md#register-the-application-package) 및 [등록 취소](service-fabric-deploy-remove-applications.md#unregister-an-application-type)할 수 있습니다.

배포 메커니즘은 압축된 패키지와 압축되지 않은 패키지에 대해 모두 동일합니다. 압축된 패키지는 클러스터 이미지 저장소에 그대로 저장되며, 먼저 노드에서 압축이 풀린 후에 응용 프로그램이 실행됩니다.
압축은 유효한 Service Fabric 패키지를 압축된 버전으로 바꿉니다. 폴더에 대한 쓰기 권한을 허용해야 합니다. 이미 압축된 패키지에 압축을 실행하면 아무런 변화가 없습니다. 

`CompressPackage` 스위치와 함께 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) PowerShell 명령을 실행하여 패키지를 압축할 수 있습니다. `UncompressPackage` 스위치를 사용하면 동일한 명령으로 패키지의 압축을 풀 수 있습니다.

다음 명령은 패키지를 이미지 저장소에 복사하지 않고 압축합니다. `SkipCopy` 플래그 없이 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage)를 사용하여 필요에 따라 하나 이상의 Service Fabric 클러스터에 압축된 패키지를 복사할 수 있습니다. 이제 패키지에 `code`, `config` 및 `data` 패키지의 압축 파일이 포함됩니다. 응용 프로그램 매니페스트 및 서비스 매니페스트는 많은 내부 작업(특정 유효성 검사를 위한 패키지 공유, 응용 프로그램 유형 이름 및 버전 추출 등)에 필요하기 때문에 압축되지 않습니다.
매니페스트를 압축하면 이러한 작업이 비효율적으로 수행됩니다.

```
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
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

또는 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage)를 사용하여 한번에 패키지를 압축하고 복사할 수 있습니다.
패키지가 크면 패키지를 압축하고 클러스터에 업로드하는 데 걸리는 시간을 충분히 허용할 만큼 긴 시간 제한을 제공합니다.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

내부적으로 Service Fabric은 유효성 검사를 위해 응용 프로그램 패키지에 대한 체크섬을 계산합니다. 압축을 사용할 때 체크섬은 각 패키지의 압축된 버전에서 계산됩니다.
압축되지 않은 버전의 응용 프로그램 패키지를 복사했고 동일한 패키지에 대해 압축을 사용하려는 경우 체크섬 불일치가 발생하지 않도록 응용 프로그램 매니페스트 버전을 변경해야 합니다.
마찬가지로 압축된 버전의 패키지를 업로드한 경우 압축되지 않은 패키지를 사용하도록 응용 프로그램 매니페스트 버전을 업데이트해야 합니다.

이제 패키지를 올바르게 패키지하고, 유효성을 검사하고, 압축하여(필요한 경우) 하나 이상의 Service Fabric 클러스터에 [배포](service-fabric-deploy-remove-applications.md)할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계
[응용 프로그램 배포 및 제거][10]에서는 PowerShell을 사용하여 응용 프로그램 인스턴스를 관리하는 방법을 설명합니다.

[여러 환경에 대한 응용 프로그램 매개 변수 관리][11]에서는 여러 응용 프로그램 인스턴스의 매개 변수 및 환경 변수를 구성하는 방법을 설명합니다.

[응용 프로그램에 대한 보안 정책 구성][12]에서는 액세스를 제한하는 보안 정책에 따라 서비스를 실행하는 방법을 설명합니다.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

