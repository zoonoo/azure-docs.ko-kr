---
title: "Azure 마이크로 서비스에 대한 개발 환경 설정 | Microsoft Docs"
description: "런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 응용 프로그램을 빌드할 수 있습니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 91049ee124999269a326106cbd6fc211cc027d26
ms.lasthandoff: 03/22/2017


---
# <a name="prepare-your-development-environment"></a>개발 환경 준비
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 개발 컴퓨터에서 [Azure Service Fabric 응용 프로그램][1]을 빌드 및 실행하려면 런타임, SDK, 도구를 설치해야 합니다. 또한 SDK에 포함된 Windows PowerShell 스크립트의 실행을 사용하도록 설정해야 합니다.

## <a name="prerequisites"></a>선행 조건
### <a name="supported-operating-system-versions"></a>지원되는 운영 체제 버전
개발을 위해 다음 운영 체제 버전이 지원됩니다.

* 윈도우 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* 윈도우 10

> [!NOTE]
> Windows 7은 기본적으로 Windows PowerShell 2.0만을 포함합니다. 서비스 패브릭 PowerShell cmdlet에는 PowerShell 3.0 이상이 필요합니다. Microsoft 다운로드 센터에서 [Windows PowerShell 5.0을 다운로드][powershell5-download]할 수 있습니다.
> 
> 

## <a name="install-the-sdk-and-tools"></a>SDK 및 도구 설치
### <a name="to-use-visual-studio-2017"></a>Visual Studio 2017을 사용하려면 다음을 수행합니다.
Service Fabric 도구는 Visual Studio 2017의 Azure 개발 및 관리 워크로드의 일부입니다. 이 워크로드를 Visual Studio 설치의 일부로 사용하도록 설정해야 합니다.
또한 웹 플랫폼 설치 관리자를 사용하여 Microsoft Azure Service Fabric SDK를 설치해야 합니다.

* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 사용(Visual Studio 2015 업데이트 2 이상 필요)
Visual Studio 2015의 경우 Service Fabric 도구는 웹 플랫폼 설치 관리자를 사용하여 SDK와 함께 설치됩니다.

* [Microsoft Azure Service Fabric SDK 및 도구 설치][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK 설치만
SDK만 필요한 경우 다음 패키지를 설치할 수 있습니다.
* [Microsoft Azure Service Fabric SDK 설치][core-sdk]

> [!WARNING]
> 고객들이 이러한 시작 링크를 사용할 때 또는 이들 링크를 Chrome 브라우저에서 사용했을 때 설치하는 동안 오류를 보고했습니다. 이러한 오류는 웹 플랫폼 설치 관리자에서 알려진 문제로 현재 해결 중입니다.  다음 해결 방법을 시도해 보세요.
>- Internet Explorer 또는 Edge 브라우저에서 위의 링크를 실행합니다. 또는
>- 시작 메뉴에서 Web Platform Installer를 시작하고, "Service Fabric"을 검색하여 SDK를 설치합니다.
> 
> 불편을 끼쳐드려 죄송합니다. 

현재 버전은 다음과 같습니다.
* Service Fabric SDK 2.5.216
* Service Fabric 런타임 5.5.216
* Visual Studio 2015 도구 1.5.50311.1

지원되는 버전 목록은 [Service Fabric 지원](service-fabric-support.md)을 참조하세요.

## <a name="enable-powershell-script-execution"></a>PowerShell 스크립트 실행 활성화
서비스 패브릭은 로컬 개발 클러스터를 만들고 Visual Studio에서 응용 프로그램을 배포하기 위해 Windows PowerShell 스크립트를 사용합니다. 기본적으로 Windows에서는 이러한 스크립트의 실행을 차단합니다. 따라서 이러한 스크립트를 사용하려면 PowerShell 실행 정책을 수정해야 합니다. 관리자로 PowerShell을 열고 다음 명령을 입력합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>다음 단계
개발 환경의 설정을 마쳤으므로 앱을 빌드하고 실행하기 시작할 수 있습니다.

* [Visual Studio에서 서비스 패브릭 응용 프로그램 처음 만들기](service-fabric-create-your-first-application-in-visual-studio.md)
* [로컬 클러스터에서 응용 프로그램을 배포하고 관리하는 방법 알아보기](service-fabric-get-started-with-a-local-cluster.md)
* [프로그래밍 모델에 대해 알아보기: Reliable Services 및 Reliable Actors](service-fabric-choose-framework.md)
* [GitHub의 서비스 패브릭 코드 샘플 확인](https://aka.ms/servicefabricsamples)
* [서비스 패브릭 탐색기를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)
* [서비스 패브릭 학습 경로를 따라 플랫폼에 대한 광범위한 소개 가져오기](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric 캠페인 페이지"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 링크"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 링크"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 링크"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

