---
title: "MyDriving Azure IoT 예제: 빌드하기 | Microsoft Docs"
description: "스트림 분석, 기계 학습, 이벤트 허브를 비롯한 Microsoft Azure를 사용하여 IoT 시스템을 설계하는 방법을 종합적으로 보여주는 앱을 빌드합니다."
services: 
documentationcenter: .net
suite: 
author: harikmenon
manager: douge
ms.assetid: c2fcd6ee-3bbe-43d1-a066-dce52cc3a53d
ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 06/30/2017
ms.author: harikm
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: c4b19cc76ca11f606ca8af6b0f3277b5aa46ac5a
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017


---
# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>사용자 환경에 MyDriving 솔루션 빌드 및 배포
MyDriving은 자동차에서 데이터를 수집하고, 기계 학습을 사용하여 처리한 후 휴대폰에 표시하는 사물 인터넷(IoT) 솔루션입니다. 백 엔드는 Microsoft Azure에서 제공하는 다양한 서비스로 구성됩니다. 클라이언트는 Android, iOS 또는 Windows 10 휴대폰일 수 있습니다.

자신만의 IoT 시스템 만들기를 시작하는 사용자를 지원하기 위해 MyDriving 솔루션을 만들었습니다. [GitHub의 MyDriving 리포지토리](https://github.com/Azure-Samples/MyDriving)에서 Azure 계정에 백 엔드 아키텍처를 배포하는 Azure Resource Manager 스크립트를 가져올 수 있습니다. 해당 지점에서 다양한 서비스를 다시 구성하고 사용자의 데이터 등에 맞게 해당 쿼리를 수정할 수 있습니다. MyDriving 리포지토리에서 이러한 스크립트와 모바일 앱, Azure 앱 서비스 API 프로젝트 등에 대한 코드를 함께 찾을 수 있습니다.

앱을 아직 시도해보지 않은 경우 [시작 가이드](iot-solution-get-started.md)를 살펴보세요.

[MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)에 아키텍처에 대한 자세한 설명이 나와 있습니다. 요약하면, 유사한 프로젝트를 만들기 위해 설정하는 여러 가지 항목이 있습니다.

* **클라이언트 앱**은 Android, iOS 및 Windows 10 휴대폰에서 실행됩니다. Xamarin 플랫폼을 사용하여 코드 대부분을 공유합니다. 코드는 GitHub에서 `src/MobileApp` 아래 저장됩니다. 앱은 실제로 두 가지 고유한 기능을 수행합니다.
  * 온보드 진단(OBD) 장치 및 고유한 위치 서비스에서 시스템의 클라우드 백 엔드로 원격 분석을 릴레이합니다.
  * 사용자가 자신의 기록된 주행 기록에 대해 쿼리할 수 있는 사용자 인터페이스입니다.
* **클라우드 서비스** 는 실시간으로 주행 기록 데이터를 수집하고 처리합니다. 이 서비스를 구성하는 기본 작업은 다양한 Azure 서비스를 선택, 매개 변수화 및 연결하는 것입니다. 일부에는 들어오는 데이터를 필터링 및 처리하는 스크립트가 필요합니다. Azure Resource Manager 템플릿을 사용하여 모든 부분을 구성합니다.
* **모바일 서비스 앱** 은 장치 앱의 사용자 인터페이스 부분 뒤에 있는 웹 서비스입니다. 기본 작업은 저장된 처리 데이터의 데이터베이스를 쿼리하는 것입니다. 해당 코드는 GitHub에서 `src/MobileAppService`아래에 있습니다.
* **Xamarin이 포함된 Visual Studio** 가 개발 환경입니다. Xamarin - Visual Studio의 구성 요소 및 독립 실행형 IDE(통합 개발 환경)라는 두 형태로 존재하며 플랫폼 간 장치 코드를 작성하는 데 사용됩니다. iOS 코드를 작성하려면 OS X 컴퓨터에서 실행되는 Xamarin의 인스턴스가 있어야 합니다. 필요한 경우 Visual Studio에서 관리하는 에이전트로 실행될 수 있습니다.
* **단위 테스트** 는 Xamarin 테스트 클라우드에서 수행됩니다.
* **GitHub** 는 모든 코드, 스크립트 및 템플릿이 저장되는 리포지토리입니다.
* **Visual Studio Team Services** 는 웹 서비스 및 장치 앱의 연속 빌드 및 테스트를 관리하는 데 사용하는 클라우드 서비스입니다.
* **HockeyApp** 은 장치 코드의 릴리스를 배포하는 데 사용됩니다. 또한 충돌 및 사용 보고서 및 사용자 피드백을 수집합니다.
* **Visual Studio Application Insights** 는 모바일 웹 서비스를 모니터링합니다.

이제 이 모든 항목을 설정하는 방법을 살펴보겠습니다. 

> [!NOTE] 
> 다음의 많은 단계는 선택 사항입니다.
>
>

## <a name="sign-up-for-accounts"></a>계정 등록
* [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). 이 무료 프로그램은 Visual Studio, Visual Studio Team Services, Azure 등 많은 개발자 도구 및 서비스에 대한 간편한 액세스를 제공합니다. 12개월 동안 Azure에서 월간 $25 크레딧을 부여합니다. 또한 Pluralsight 교육 및 Xamarin University에 대한 구독도 포함합니다. [Azure](https://azure.com) 및 [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx)의 무료 계층과 별도로 등록할 수도 있으나 이 경우 Azure 크레딧은 제공되지 않습니다.
* [HockeyApp](https://rink.hockeyapp.net/) (선택 사항) - 모바일 앱의 테스트 배포를 관리하고 원격 분석을 수집합니다.
* [Xamarin](https://xamarin.com/)(필수) - 모바일 앱을 빌드하고 [Xamarin 테스트 클라우드](https://xamarin.com/test-cloud)에서 디버그 실행 및 테스트를 수행합니다.
* [GitHub](https://github.com/Azure-Samples/MyDriving/) (선택 사항) - 사용자 고유의 코드를 위한 무료 공용 리포지토리를 만듭니다(사설 리포지토리는 유료임). 또는 Visual Studio Team Services에서 사설 리포지토리에 대해 기본 요금제를 사용합니다.
* [Power BI](https://powerbi.microsoft.com/) (옵션) - 전체 시스템 간의 데이터에 대해 다양한 시각화를 만듭니다.

> [!NOTE]
> [GitHub MyDriving 리포지토리](https://github.com/Azure-Samples/MyDriving)에서 MyDriving 코드에 액세스하는 데 GitHub 계정이 필요하지 않습니다.
> 
> 

## <a name="install-development-tools"></a>개발 도구 설치
다음 설치 프로그램은 Azure 백 엔드를 통해 iOS, Android 및 Windows 10 모바일 플랫폼 간 앱의 완전한 솔루션을 개발하기 위한 것입니다.

또는 Azure 백 엔드에서 작업하지 않는 경우 Mac 또는 Windows에서 Xamarin Studio를 사용하여 모바일 앱을 개발할 수 있습니다.

[이 설치 프로그램에 대한 자세한 설명](https://msdn.microsoft.com/library/mt613162.aspx)이 있습니다.

### <a name="windows-development-machine"></a>Windows 개발 컴퓨터
Windows에서 중앙 도구는 Visual Studio이며 여기에서 Android 및 Windows 용 MyDriving 앱, 앱 서비스 API 프로젝트 및 마이크로 서비스 확장을 사용합니다.

Xamarin, Git, 에뮬레이터 및 기타 유용한 구성 요소가 Visual Studio에 모두 통합되어 있습니다.

설치:

* [Xamarin 포함 Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs) (모든 버전--커뮤니티는 무료)
* [범용 Windows 플랫폼용 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Windows 10 Mobile 코드를 작성하는 데 필요합니다.
* [Azure SDK for Visual Studio](https://www.visualstudio.com/vs/azure-tools/) Azure에서 앱을 실행하기 위한 SDK와 Azure를 관리하기 위한 명령줄 도구를 제공합니다.
* [Azure 서비스 패브릭 SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). [마이크로 서비스](../service-fabric/service-fabric-get-started.md) 확장을 빌드하는 데 필요합니다.

올바른 Visual Studio 확장이 있는지 확인합니다. **도구** 아래를 확인해 보면 **Android, iOS, Xamarin…**이 표시됩니다. 그렇지 않은 경우 Visual Studio를 열고 Xamarin을 검색한 후 지시에 따라 설치합니다. 또한 **Windows용 Git**이 설치되어 있는지도 확인합니다. 설치되어 있지 않은 경우 Visual Studio를 열고 검색한 후 지시에 따라 설치합니다. 

### <a name="mac-development-machine"></a>Mac 개발 컴퓨터
iOS를 개발하는 데 Mac(Yosemite 이상)이 필요합니다. 모든 코드를 개발 및 관리하는 데 Windows에서 Xamarin 포함 Visual Studio를 사용하더라도, Xamarin은 iOS 코드를 작성 및 서명하는 데 Mac에 설치된 에이전트를 사용합니다.

![Windows에서 개발 및 Mac에서 빌드](./media/iot-solution-build-system/image1.png)

(또는 Mac에서 플랫폼 간 앱을 개발하는 데 Xamarin Studio를 직접 사용할 수 있습니다.)

대상 플랫폼으로 iOS를 포함하지 않으려는 경우 Mac은 필요하지 않습니다.

설치:

* [iOS용 Xamarin Studio](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). Windows 가상 컴퓨터를 실행하는 Mac에서 Visual Studio 및 Xamarin을 설정할 수도 있습니다. MSDN의 [Mac 사용자용 설정, 설치 및 확인](https://msdn.microsoft.com/library/mt488770.aspx) 을 참조하세요.
* [Azure 개발 도구](https://azure.microsoft.com/downloads/) (선택 사항).

Mac에서 원격 로그인을 사용하도록 설정합니다. **시스템 기본 설정** > **공유**를 연 다음 **원격 로그인**을 선택합니다.

Windows의 Visual Studio에서 iOS 프로젝트를 열 때 Xamarin 플러그 인에서 Mac의 ID를 묻는 메시지를 표시합니다.

## <a name="fetch-the-github-repository"></a>Github 리포지토리 가져오기
GitHub, Visual Studio 또는 다른 Git 클라이언트에서 [ZIP 다운로드](https://github.com/Azure-Samples/MyDriving) 단추를 사용하여 **GitHub MyDriving 리포지토리** 의 로컬 복사본을 가져옵니다.

짧은 경로 이름(예: C:\\code)을 사용하여 폴더에 파일의 압축을 풉니다.

또는 최신 상태로 유지하거나 코드에 적용하려면 다음과 같이 리포지토리를 복제합니다.

**git clone https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>Bing 지도 API 키 가져오기
[Bing 지도 API 키를 등록합니다](https://msdn.microsoft.com/library/ff428642.aspx).

`src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`의 22줄에서 이를 대체해야 합니다.

## <a name="build-the-demo-app"></a>데모 앱 빌드
Visual Studio에서 이러한 솔루션을 엽니다.

* src\MobileApps\MyDriving.sln
* src\MobileAppService\MyDrivingService.sln
* src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.sln

다음과 같은 프롬프트가 표시됩니다.

* 잠재적으로 신뢰할 수 없는 프로젝트를 신뢰합니다. 계속하려는 경우 열도록 선택합니다.
* 새로운 Windows 10 컴퓨터에서 작업하는 경우 개발자 모드를 설정합니다.
* Xamarin 자격 증명을 제공합니다.
* Xamarin Mac에 연결합니다. Mac이 없는 경우 Visual Studio에서 iOS 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **프로젝트 언로드**를 선택합니다.

솔루션을 다시 빌드합니다.

빌드하는 데 문제가 있다면 찾은 쿼크에 대한 솔루션을 수행합니다.

* *VINLookupApplication 프로젝트가 로드되지 않음*: [Visual Studio용 Azure SDK](https://www.visualstudio.com/vs/azure-tools/)가 설치되어 있는지 확인합니다.
* *Service Fabric 프로젝트가 빌드되지 않음*: 먼저, 인터페이스 프로젝트를 빌드하고 Service Fabric SDK가 설치되어 있는지 확인합니다.
* *Android 앱이 빌드되지 않음*:
  
  * **도구** > **Android** > **Android SDK Manager**를 열고 Android 6(API 23)/SDK 플랫폼이 설치되어 있는지 확인합니다.
  * 다음 디렉터리를 삭제한 다음 다시 빌드합니다.<br/>
    `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>코드 살펴보기
솔루션에서 다음을 확인할 수 있습니다.

* Azure 확장: 서비스 패브릭.
* Azure HDInsight: Azure에서 주행 데이터를 처리하는 스크립트.
* 모바일 앱: 장치 앱.
* MobileAppsService/MyDrivingService: 웹 백 엔드.
* Power BI: 보고서 정의.
* Scripts:
  
  * Resource Manager: Azure 리소스를 작성하는 템플릿.
  * PowerShell: Resource Manager 템플릿을 실행하는 스크립트.
  * Azure SQL 데이터베이스: 데이터베이스 디버깅.
* SQL 데이터베이스: CreateTables: 스키마 정의.
* Azure 스트림 분석: 들어오는 데이터 스트림을 변환하는 쿼리.

## <a name="run-the-apps-in-development-mode"></a>개발 모드에서 앱 실행
사용 중인 장치에 따라 앱을 실행하는 작업을 수행합니다.

* 백 엔드: MyDrivingService를 시작 프로젝트로 설정하고 백 엔드 웹 서비스를 실행하려면 F5 키를 누릅니다. 그러면 API 목록 브라우저 보기가 열립니다.
* 모바일 클라이언트: [모바일 앱이 Xamarin에서 개발됩니다](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
  
  * Android: 자세한 내용은 [Xamarin에서 Android 디버깅](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)을 참조하세요.
  * iOS: 자세한 내용은 [iOS에서 디버깅](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)을 참조하세요.
  * Windows Phone: 자세한 내용은 [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)을 참조하세요.

## <a name="upload-the-mobile-app-to-hockeyapp"></a>HockeyApp에 모바일 앱 업로드
HockeyApp은 Android, iOS 또는 Windows 앱의 배포를 관리하여 사용자를 테스트하고 사용자에게 새 릴리스를 알려 줍니다. 또한 유용한 충돌 보고서, 스크린샷을 포함한 사용자 피드백, 사용 현황 메트릭도 수집합니다.

[업로드하여 시작](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) 합니다. 그런 다음 개발 컴퓨터에서 [HockeyApp](https://rink.hockeyapp.net) 에 로그인합니다. 개발자 대시보드에서 **새 앱**을 클릭한 다음 작성한 파일을 창으로 끕니다. (나중에 이 작업을 수행하도록 빌드 서비스를 자동화할 수 있습니다.)

이제 앱 대시보드가 표시됩니다.

![앱 대시보드의 개요 탭](./media/iot-solution-build-system/image2.png)

앱이 실행되는 각 플랫폼에 대해 이 프로세스를 반복합니다. 그런 다음 다음을 수행할 수 있습니다.

* 대시보드에서 [앱 ID](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) 를 사용하여 앱에서 충돌 데이터 및 피드백을 보냅니다. MyDriving에서 src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs에 있는 ID를 업데이트합니다.
* [테스트 사용자를 초대합니다](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). 테스터 사용자를 모집하는 URL을 받습니다. 팀에 등록하여 앱을 다운로드하고 피드백을 보낼 수 있습니다.
* 보다 개방된 베타 릴리스를 선호하는 경우 배포를 공용으로 설정합니다. **Manage App** > **Distribution** > **Download = Public**을 클릭합니다. 이제 모든 사용자가 앱을 다운로드하고 피드백을 보낼 수 있으며 새 버전이 게시되면 알림을 보게 됩니다. 또한 충돌 보고서도 받을 수 있습니다.
  
   ![대시보드의 팀](./media/iot-solution-build-system/image3.png)
* [충돌 보고서를 Visual Studio Team Services에 링크합니다](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). **Manage App** > **Visual Studio Team Services**를 클릭합니다. HockeyApp은 충돌 보고서가 있거나 피드백을 받으면 Team Services에 작업 항목을 자동으로 만들 수 있습니다.

[HockeyApp 사이트](https://hockeyapp.net)에서 자세히 알아보세요.

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>Xamarin 테스트 클라우드에서 모바일 앱 테스트
[Xamarin 테스트 클라우드](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) 는 클라우드의 실제 장치에서 UI 테스트를 자동화합니다. NUnit 프레임워크를 사용하여 사용자 인터페이스를 통해 앱을 실행하는 테스트를 작성합니다.

Xamarin을 사용하려면 [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK를 NuGet 패키지로 제공되는 앱에 통합합니다. 데모 앱에 있으며 Xamarin 템플릿으로 새 테스트 프로젝트를 만들 때 포함됩니다.

![인터페이스의 플랫폼 간 SDK를 찾을 수 있는 위치](./media/iot-solution-build-system/image4.png)

예제 테스트 프로젝트는 리포지토리에 앱과 함께 포함되어 있습니다. [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService)에서 [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/를 살펴봅니다.

Visual Studio Team Services 빌드를 사용하는 경우 간편하게 Xamarin UI 단위 테스트를 작성하고 빌드의 일부로 실행합니다.

## <a name="deploy-azure-services"></a>Azure 서비스 배포
**scripts/README.md**에서 Azure 서비스 및 Team Services 빌드 서비스의 자동 배포를 수행하는 자세한 지침을 참조하세요.

Microsoft Azure는 클라우드 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 서비스를 제공합니다. 대부분은 개별적으로 사용할 수 있지만(예: 앱 서비스/웹앱) MyDriving에서 사용하는 것처럼 통합된 시스템을 구성하도록 상호 연결된 경우 가장 효과적입니다.

Azure 서비스를 만들고 수동으로 상호 연결할 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 훨씬 빠르고 안정적입니다. [Resource Manager](../azure-resource-manager/resource-group-overview.md) 는 솔루션의 리소스 배포와 이들 간의 상호 연결을 자동화합니다.

[scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM)아래 GitHub 리포지토리에 MyDriving 시스템에 대한 템플릿이 있습니다. 아키텍처 내의 다양한 서비스가 어떻게 상호 연결되는지 보여주는 포괄적이고 간결한 보기를 제공합니다. 이에 대해서는 [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)에서 자세히 설명하지만 템플릿 자체를 읽어 보는 것만으로 상당 부분 학습할 수 있습니다.

> [!NOTE]
> 대부분의 Azure 서비스는 가격 책정 계층에 따른 관련 비용을 포함합니다. Azure를 처음 접하는 경우 [무료로 사용해 볼 수 있습니다](https://azure.microsoft.com/free/). 그러나 MyDriving 시스템에서 특정 구성 요소를 사용할 계획이 아니라면 비용이 발생하지 않도록 제거해야 합니다. 이 문서의 뒷부분에 나오는 "예상 운영 비용" 섹션은 일반 서비스 비용의 요약을 제공합니다.
> 
> 

### <a name="edit-the-template"></a>템플릿 편집
배포를 사용자 지정하거나 불필요한 구성 요소를 제거하거나 다른 구성 요소를 추가하려면 먼저 변경할 scenario\_complete.params.json 및 scenario\_complete.json의 복사본을 만듭니다.

scenario\_complete.params.json 파일을 사용하여 다음 표에 설명된 대로 저장소 복제 유형 또는 서비스 SKU와 같은 다양한 기본값을 재정의할 수 있습니다. 이 기본값은 가장 저렴한 비용 옵션을 선택합니다.

| **매개 변수** | **설명** | **기본값** |
| --- | --- | --- |
| IoT Hub SKU |Azure IoT Hub 서비스에 대한 계층 |F1 |
| 저장소 계정 유형 |저장소 복제 유형 |표준 LRS |
| SQL 서비스 목표 |동시성 슬롯 사용량 |DW100 |
| 호스팅 계획 SKU |앱 서비스에 대한 서비스 계획 |F1 |

scenario\_complete.json에서

* "baseName"을 검색하고 원하는 이름으로 변경합니다.
* "만들기"를 검색합니다. 각 섹션에서 리소스를 만듭니다.
* sqlServerAdminLogin 및 sqlServerAdminPassword를 적합한 값으로 설정합니다.
* 리소스를 만드는 섹션을 삭제하기 전에 파일의 다른 부분에서 해당 이름을 검색하여 종속 여부를 확인합니다. 서비스를 만드는 각 섹션에는 해당 종속성을 나열하는 *dependsOn* 섹션이 있습니다.

다음은 템플릿을 구성하는 것입니다. 세부 정보는 [참조 가이드](http://aka.ms/mydrivingdocs)를 참조하세요.

| **서비스** | **설명 및 세부 정보** |
| --- | --- |
| 저장소 계정 |템플릿은 세 개의 계정을 만듭니다. |
| - SQL 데이터베이스: Stream Analytics에서 집계된 원격 분석을 수신하고 해당 데이터를 API 끝점을 통해 노출하는 Azure App Service 테이블에 대한 백업 저장소 역할을 합니다. | |
| - Blob Storage: HDInisight에서 처리할 다른 Stream Analytics 작업에서 얻은 과거 데이터를 누적합니다. | |
| - SQL Database: HDInsight에서 Power BI에 사용하기 위해 처리한 결과를 수신합니다. | |
| Azure IoT Hub |연결된 각 장치로 양방향 연결을 설정합니다. MyDriving 솔루션에서 모바일 앱은 Azure IoT Hub로 데이터를 보내기 위한 현장 게이트웨이 역할을 합니다. 그런 다음 Azure IoT Hub는 스트림 분석에 대한 입력으로 사용됩니다. |
| Azure 이벤트 허브 |출력을 Azure 서비스 패브릭으로 만든 확장으로 큐에 대기시키는 스트림 분석 작업에 대한 출력입니다. |
| Azure SQL 데이터 웨어하우스 | |
| 스트림 분석 작업 |입력 및 출력을 쿼리와 연결합니다. 앱 서비스 API, Azure 기계 학습, 확장 및 Power BI에 대한 실시간 및 과거 데이터를 집계하는 데 사용됩니다. |
| 기계 학습 작업 영역 |실험, R 코드, API 서비스를 포함합니다. |
| Azure 데이터 팩터리 |예약된 기계 학습 재학습입니다. |
| 서비스 패브릭 호스팅 계획 |확장용 |
| 앱 서비스("모바일 앱") |모바일 앱에 대한 끝점을 제공하는 모바일 앱 API 프로젝트를 호스팅합니다. Visual Studio에서 API 코드를 앱 서비스에 배포해야 합니다. |
| 경고 규칙 |앱 응답이 오류를 표시하면 전자 메일을 보냅니다. |
| Application Insights |앱 서비스에서 API의 성능을 모니터링합니다. Visual Studio에서 연결을 구성해야 합니다. |
| Azure 키 자격 증명 모음 |웹 서비스 클러스터 인증서를 저장합니다. |

### <a name="run-the-template"></a>템플릿 실행
**scripts/README.md**에 템플릿 실행을 위한 자세한 지침이 있습니다.

스크립트를 사용하여 자신의 Azure 계정에서 이러한 모든 서비스를 프로비전하려면 다음 중 하나를 수행합니다.

* PowerShell 사용:
  
  ```
  
  cd scripts/PowerShell;
  deploy.ps1 *location* *resourceGroupName*
  ```
  
  * *location*은 `North Europe` 또는 `West US` 등의 [Azure 위치](https://azure.microsoft.com/regions/)입니다. `Get-AzureLocation` 을(를) 사용하여 사용할 수 있는 위치의 목록을 찾을 수 있습니다.
  * *resourceGroupName* 은 모든 리소스가 속하는 그룹에 지정하려는 이름입니다. 리소스 작업을 완료했으면 이 그룹을 삭제하여 모두 함께 삭제할 수 있습니다.
* Bash로 DeploymentScripts/Bash/deploy.sh를 실행합니다.
* Visual Studio 솔루션 DeploymentScripts/VS/DeployARM.sln을 열고 빌드합니다.

템플릿이 실행될 때마다 새 이름의 새 리소스 집합이 만들어집니다. 리소스를 삭제하려면 포털로 이동하고 리소스 그룹을 삭제합니다.

어떠한 이유로 스크립트에 실패하면 다시 실행할 수 있습니다.

스크립트는 Visual Studio Team Services에서 연속 통합을 구성하는 옵션을 제공합니다. Team Services 프로젝트를 설정하면 URL: https://yourAccountName.visualstudio.com이 생깁니다. 메시지가 표시되면 전체 URL을 입력합니다. Team Services 프로젝트에 대한 기존 또는 신규 이름을 지정할 수 있습니다.

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>빌드를 설정하고 Visual Studio Team Services에서 정의를 테스트합니다.
이 프로젝트에서는 대부분 빌드 및 테스트 기능을 위해 Team Services를 사용합니다. 하지만 Kanban 보드를 사용한 작업 관리, 작업 및 소스 제어와 통합된 코드 검토 및 제어 빌드와 같은 뛰어난 공동 작업 지원도 제공합니다. GitHub, Xamarin, HockeyApp, 물론 Visual Studio와 같은 다른 도구와도 잘 통합됩니다. 웹 인터페이스 또는 Visual Studio를 통해 액세스할 수 있으며 언제든지 어떤 것이든 편한 방법을 선택하면 됩니다.

빌드 및 릴리스 정의 단계에서는 Team Services [마켓플레이스](https://marketplace.visualstudio.com/VSTS)에서 사용할 수 있는 다양한 플러그 인 서비스를 사용합니다. 명령줄을 실행하거나 파일을 복사하는 기본 유틸리티 외에도 Xamarin, Android 및 기타 공급 업체에서 빌드를 호출하고 HockeyApp과 연결하는 서비스가 있습니다.

![팀 서비스의 빌드 옵션](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>빌드 정의
각 주 대상에 대한 빌드를 정의했습니다. 기능 및 회귀 테스트에 대한 변형이 있습니다. 다음이 제공됩니다.

* MyDriving.Services(모바일 앱용 백 엔드 웹앱)
* MyDriving.Xamarin.Android
  
  * MyDriving.Xamarin.Android-Feature
  * MyDriving.Xamarin.Android-Regression
* MyDriving.Xamarin.iOS
  
  * MyDriving.Xamarin.iOS-Feature
  * MyDriving.Xamarin.iOS-Regression
* MyDriving.Xamarin.UWP
  
  * MyDriving.Xamarin.UWP-Feature
  * MyDriving.Xamarin.UWP-Regression

구성에 대한 전체 내용을 보려면 [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)의 4.7 섹션, “빌드 및 릴리스 구성”을 참조하세요. 동일한 일반 패턴을 따릅니다. 스크립트:

1. NuGet 패키지를 복원합니다. 리포지토리에 컴파일된 코드가 유지되지 않으므로 각 빌드의 첫 번째 단계는 필요한 NuGet 패키지를 복원하는 것입니다.
2. 라이선스를 활성화합니다. 클라우드에서 빌드가 수행되므로 라이선스가 필요한 경우 특히 Xamarin 빌드 서비스에서는 현재 빌드 컴퓨터에서 라이선스를 활성화해야 합니다. 그런 다음 즉시 비활성화하여 다른 컴퓨터에서 사용할 수 있도록 합니다.
3. 적절한 서비스를 사용하여 빌드합니다. 모바일 앱에 대해 Xamarin 빌드를 사용하고 백 엔드 웹 서비스에 대해 Visual Studio 빌드를 사용합니다.
4. 테스트를 작성합니다.
5. 테스트를 실행합니다. Xamarin 테스트 클라우드에서 모바일 앱 테스트를 실행합니다.
6. 빌드 결과를 저장 위치에 게시합니다.

기본 빌드에 대한 트리거를 연속 통합으로 설정합니다. 즉, 빌드는 코드가 마스터 분기로 체크 인될 때마다 실행됩니다.

![트리거가 연속 통합으로 설정된 인터페이스](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>릴리스 정의
릴리스 정의는 거의 동일한 방식으로 설정됩니다.

웹 서비스의 경우 배포를 Azure 웹앱 설정합니다.

![Azure 웹앱으로 배포를 설정하기 위한 인터페이스](./media/iot-solution-build-system/image7.png)

그리고 릴리스 트리거를 연속 배포로 설정합니다. 즉, 모든 체크 인 후 성공적으로 빌드되면 웹앱이 업데이트됩니다.

![릴리스 트리거를 연속 배포로 설정하기 위한 인터페이스](./media/iot-solution-build-system/image8.png)

모바일 앱의 경우 HockeyApp에 배포합니다.

![HockeyApp에 모바일 앱을 배포하기 위한 인터페이스](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>Application Insights를 사용하여 원격 분석 탐색
[Application Insights](../application-insights/app-insights-overview.md) 는 웹 서비스의 성능 및 사용 현황에 대한 원격 분석을 수집합니다. Application Insights SDK는 Azure의 Application Insights 리소스로 서비스의 원격 분석을 보냅니다.

템플릿으로 설정할 Application Insights 리소스로 이동합니다. 여기서 [모바일 앱 서비스 프로젝트](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService)의 성능 차트를 탐색할 수 있습니다. 서버 요청 및 응답 시간, 오류, 예외 수가 표시됩니다. 또한 종속성 응답 시간 차트도 제공됩니다. 즉, 데이터베이스 및 기계 학습과 같은 REST API에 대한 호출입니다. 성능 문제가 있는 경우 시스템의 어느 부분에서 문제가 발생하는지 확인할 수 있습니다.

![예제 성능 차트](./media/iot-solution-build-system/image11.png)

웹 서비스를 수동으로 설정하는 경우 동일한 차트를 쉽게 얻을 수 있습니다. 웹 서비스 블레이드에서 **도구** > **확장** > **추가**를 클릭합니다. **Application Insights**를 선택합니다.

![차트를 가져오도록 Application Insights를 선택하기 위한 인터페이스](./media/iot-solution-build-system/image12.png)

이 기능은 Application Insights SDK를 사용하여 응용 프로그램을 계측하는 방식으로 작동합니다.

개발 시간에 [Application Insights SDK를 추가](../application-insights/app-insights-asp-net.md) 하여 사용자 지정 원격 분석(또는 Azure 외부에서 실행되는 응용 프로그램 계측)을 추가할 수 있습니다. 사용자의 평균 주행 거리 또는 총 주행 거리처럼 응용 프로그램에 종속된 메트릭을 기록하는 데 유용합니다. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **Application Insights 추가**를 선택합니다.

![사용자 지정 원격 분석을 추가하도록 Application Insights 추가를 선택하기 위한 인터페이스](./media/iot-solution-build-system/image10.png)

Application Insights는 오류 응답 수가 비정상적인 것으로 나타나는 경우 경고 전자 메일을 보냅니다. 응답 시간과 같은 다양한 메트릭에 대한 사용자 고유의 알림을 설정할 수도 있습니다.

웹 서비스가 항상 가동되어 실행 중인지 확인하기 위해 [가용성 테스트](../application-insights/app-insights-monitor-web-app-availability.md)를 설정할 수 있습니다. 이러한 테스트는 전 세계 다양한 위치에서 15분 단위로 사이트로 ping을 수행합니다. 또한 문제가 있는 경우 전자 메일을 받게 됩니다.

## <a name="estimate-operational-costs"></a>예상 운영 비용
소규모에서는 이처럼 앱을 실행하는 비용이 매우 저렴합니다. 많은 서비스가 무료의 초급 수준 계층을 포함하므로 개발 및 소규모 운영 비용이 매우 적습니다. 물론, 사용자 고유 앱에서 MyDriving에 설명된 모든 기능을 사용할 필요는 없습니다.

다음은 MyDriving에 대한 개발 구성을 설정하는 데 대략적인 예상 비용입니다. 또한 사용하지 *않았던* 몇 가지 다른 방법도 확인합니다. 이 정보는 자체 비용을 예측하므로 유용할 수 있습니다.

다음을 가정합니다.

* 5팀 이하(+ 이해 관계자 관찰)
* 약 한 달 동안 실행
* 하루에 네 개의 여정으로 100명의 사용자

> [!NOTE]
> Azure를 처음 접하는 경우 [무료 계정](https://azure.microsoft.com/free/)이 있습니다.
> 
> 

| **서비스/구성 요소** | **참고 사항** | **비용/월** |
| --- | --- | --- |
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs)와 [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>플랫폼 간 개발 환경 |Visual Studio Community. (단일 코드 기본 사항에서 플랫폼 간 설계를 위해 [Xamarin.Forms](https://xamarin.com/forms)용 [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) 필요) |$0 |
| [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>장치에 대해 양방향 데이터 연결 |8,000개 메시지 + 0.5KB/메시지 무료 |$0 |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>   고용량 스트림 데이터 처리 |사용된 동안 시간 및 스트리밍 단위당 $0.031 부과. 원하는 스트리밍 단위를 선택합니다. 강화하려면 더 늘림. |$23 |
| [기계 학습](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> 적응 응답 |$10/사용자/월. <br/>                                                                                                                                                                                 + 3시간 테스트 \* $1/테스트 시간 <br/>                                                                                                                                                           + 3.5시간 API CPU \* $2/프로덕션 CPU 시간  <br/>                                                                                                                                                          API CPU 시간은 5분/일 재학습을 가정. 단, 입력 데이터가 많아지면 증가할 수 있음.                   <br/>                                                                                                                                                                     + 400트립/일을 처리하기 위해 2분/일 |$20 |
| [App Service](https://azure.microsoft.com/pricing/details/app-service/)  <br/> 모바일 백 엔드에 대한 호스트 |B1 계층--프로덕션 웹앱 |$56 |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/> 빌드, 단위 테스트 및 릴리스 관리, 작업 관리 |사용자 에이전트, 5명의 사용자. |$0 |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>웹 서비스 및 사이트의 성능 및 사용 현황 모니터링 |무료 계층. |$0 |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> 피드백, 사용 및 충돌 데이터의 컬렉션을 더한 베타 앱 배포 |새 사용자를 위한 두 가지 무료 앱.<br/> 이후에는 $30/월. |$0 |
| [Xamarin](https://store.xamarin.com/)<br/> 여러 장치에서 균일한 플랫폼에 대한 코드 |무료 평가판. <br/>이후에는 $25/월. |$0 |
| [SQL 데이터베이스](https://azure.microsoft.com/pricing/details/sql-database/)  |기본 계층, 단일 데이터베이스 모델. |$5 |
| [Service Fabric](https://azure.microsoft.com/pricing/details/service-fabric/)(선택 사항) |로컬 클러스터를 실행합니다. |$0 |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> 스트리밍 및 정적 데이터에 대한 다양한 표시 및 조사 |무료 계층: 1GB, 10,000행/시간, 매일 새로 고침. <br/> [제한 확대](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), 추가 연결 옵션, 공동 작업을 위해 $10/사용자/월 |$0 |
| [저장소](https://azure.microsoft.com/pricing/details/storage/) |L(로컬 중복) &lt; 100 G$0.024/GB. |$3 |
| [데이터 팩터리](https://azure.microsoft.com/pricing/details/data-factory/) |활동당 $0.60 \* (8 - 5 FOC) |$2 |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>  일일 재학습 |매일 1시간 동안 $0.32/시간으로 3개 A3 노드 * 31일. |$30 |
| [이벤트 허브](https://azure.microsoft.com/pricing/details/event-hubs/) |$11/월 처리량 단위(기본) + $0.028 수신. |$11 |
| OBD 동글 | |$12 |
| **합계** | |**$157** |

자세한 내용은 다음을 참조하세요.

* [Azure 서비스 할당량 및 제한](../azure-subscription-service-limits.md#iot-hub-limits)
* Azure [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

## <a name="send-us-your-feedback"></a>사용자 의견을 보냅니다.
사용자가 자신의 IoT 시스템을 시작할 수 있도록 MyDriving을 작성했으므로 실제로 얼마나 잘 작동하는지 의견을 듣고 싶습니다. 다음의 경우 알려주세요.

* 문제 또는 도전 과제가 발생했습니다.
* 시나리오에 더 적합할 수 있는 확장 지점이 있습니다.
* 특정 요구를 수행하는 더 효율적인 방법을 찾았습니다.
* MyDriving 또는 이 설명서를 개선하기 위한 다른 제안이 있습니다.

피드백을 제공하려면 [GitHub에 문제점]을 제출하거나 아래에 의견을 남겨 주세요(en-us 버전).

여러분의 소중한 의견을 기다리고 있습니다.

## <a name="next-steps"></a>다음 단계
시스템 및 해당 구성 요소의 설계에 대한 포괄적인 설명을 제공하는 [MyDriving 참조 설명서](http://aka.ms/mydrivingdocs)를 참조하세요.


