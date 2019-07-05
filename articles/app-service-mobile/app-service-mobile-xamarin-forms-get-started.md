---
title: Xamarin.Forms을 사용하여 Mobile Apps 시작
description: 이 자습서에 따라 Mobile Apps를 사용하여 Xamarin.Forms 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: bca0f0de7de321060635459c4435525f650c7467
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446313"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Azure를 사용하여 Xamarin.Forms 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발을 위한 중앙 새 및 통합 서비스에 투자 합니다. 개발자가 사용할 수 **빌드**를 **테스트** 하 고 **배포** 연속 통합 및 배달 파이프라인을 설정 하는 서비스입니다. 개발자 상태 및 사용 하 여 해당 앱의 사용량을 모니터링할 수 있습니다, 앱을 배포한 후 합니다 **Analytics** 하 고 **진단** , 서비스를 사용 하 여 사용자와 소통 하세요를 **푸시** 서비스입니다. 개발자가 활용할 수도 있습니다 **인증** 해당 사용자를 인증 하 고 **데이터** 유지 하 고 클라우드에 앱 데이터 동기화 서비스. 체크 아웃 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started) 지금 합니다.
>

## <a name="overview"></a>개요
이 자습서에서는 Azure App Service의 Mobile Apps 기능을 백 엔드로 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.Forms 모바일 앱에 추가하는 방법을 보여줍니다. 새 Mobile Apps 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 할 일 모음 Xamarin.Forms 앱을 만듭니다.

이 자습서를 완료해야 다른 모든 Xamarin.Forms용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* Visual Studio Tools for Xamarin에서 Visual Studio 2017 이상 버전에서는 또는 mac 용 Visual Studio 참조 된 [Xamarin 설치 페이지][Install Xamarin] 지침에 대 한 합니다.

* (선택 사항) iOS 앱을 빌드하려면 Xcode 9.0 이상이 있는 Mac이 필요합니다. Mac 용 visual Studio iOS 앱 또는 Visual Studio 2017 개발에 사용할 수 있습니다 또는 나중에 사용할 수 있습니다 (으로 Mac이 네트워크에서 사용할 수 있습니다).

## <a name="create-a-new-mobile-apps-back-end"></a>새 Mobile Apps 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트와 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Xamarin.Forms 솔루션 실행

Visual Studio Tools Xamarin 솔루션을 열고 필요에 대 한 참조를 [Xamarin 설치 지침][Install Xamarin]합니다. 도구를 이미 설치한 경우 다음 단계에 따라 솔루션을 다운로드하고 엽니다.

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows 및 Mac)

1. 로 이동 합니다 [Azure portal](https://portal.azure.com/) 만든 모바일 앱으로 이동 합니다. 에 `Overview` 블레이드에서 모바일 앱에 대 한 공용 엔드포인트 URL 찾습니다. 예제-내 앱 이름 "test123"에 대 한 sitename 됩니다 https://test123.azurewebsites.net 합니다.

2. 파일을 열고 `Constants.cs` 이 폴더에서 xamarin.forms/ZUMOAPPNAME 합니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

3. `Constants.cs` 클래스를 대체 `ZUMOAPPURL` 위의 공용 끝점을 사용 하 여 변수입니다.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    됩니다.

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. 아래 지침을 따라 Android 또는 Windows 프로젝트를 실행합니다. 사용할 수 있는 네트워크로 연결된 Mac 컴퓨터가 있는 경우 iOS 프로젝트입니다.

## <a name="optional-run-the-android-project"></a>(선택 사항) Android 프로젝트 실행

이 섹션에서는 Xamarin.Android 프로젝트를 실행합니다. Android 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. Android(Droid) 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 Android 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누르거나 **시작** 단추를 클릭합니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작하려면 **실행** 메뉴를 선택한 다음, **디버깅 시작**을 선택합니다.

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호( **+** )를 선택합니다.

![Android 할 일 앱][11]

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

> [!NOTE]
> Mobile Apps 백 엔드에 액세스하는 코드는 솔루션의 공유 코드 프로젝트의 **TodoItemManager.cs** C# 파일에 있습니다.
>

## <a name="optional-run-the-ios-project"></a>(선택 사항) iOS 프로젝트 실행

이 섹션에서는 iOS 디바이스용 Xamarin.iOS 프로젝트를 실행합니다. iOS 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 iOS 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누릅니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **실행** 메뉴에서 **디버깅 시작**을 선택하여 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호( **+** )를 선택합니다.

![iOS 할 일 앱][10]

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

> [!NOTE]
> 솔루션의 공유 코드 프로젝트의 **TodoItemManager.cs** C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
>

## <a name="optional-run-the-windows-project"></a>(선택 사항) Windows 프로젝트 실행

이 섹션에서는 Windows 디바이스에 대한 Xamarin.Forms UWP(유니버설 Windows 플랫폼) 프로젝트를 실행합니다. Windows 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. UWP 프로젝트를 마우스 오른쪽 단추로 누른 다음, **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 선택한 Windows 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 Windows 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누르거나 **시작** 단추를 클릭합니다(**로컬 컴퓨터**를 읽어야 함).

> [!NOTE]
> Windows 프로젝트를 macOS에서 실행할 수 없습니다.

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호( **+** )를 선택합니다.

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

![UWP 할 일 앱][12]

> [!NOTE]
> 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 **TodoItemManager.cs** C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
>

## <a name="troubleshooting"></a>문제 해결

솔루션을 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 `Xamarin.Forms`로 업데이트하고, Android 프로젝트에서 `Xamarin.Android` 지원 패키지를 업데이트합니다. 빠른 시작 프로젝트는 항상 최신 버전을 포함하지 않을 수 있습니다.

Android 프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
