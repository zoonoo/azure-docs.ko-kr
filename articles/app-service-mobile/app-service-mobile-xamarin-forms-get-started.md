---
title: "Xamarin.Forms을 사용하여 모바일 앱 시작"
description: "이 자습서에 따라 Azure 모바일 앱을 사용하여 Xamarin.Forms 개발을 시작할 수 있습니다."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 6b8c5c2ac2e721c4d6f73c7c17f34eadc041e0c9


---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.Forms 모바일 앱에 추가하는 방법을 보여 줍니다. 새 Mobile App 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 모음* Xamarin.Forms 앱을 만듭니다.

이 자습서를 완료해야 다른 모든 Xamarin.Forms용 모바일 앱 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Xamarin이 포함된 Visual Studio입니다. 지침은 [Visual Studio 및 Xamarin을 위한 설치 및 설정](https://msdn.microsoft.com/library/mt613162.aspx) 을 참조하세요. 
* Xcode v7.0 이상 및 Xamarin Studio Community가 설치된 Mac입니다. [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx) 및 [Mac 사용자를 위한 설정, 설치 및 유효성 검사](https://msdn.microsoft.com/library/mt488770.aspx)(MSDN)를 참조하세요.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 평가](https://azure.microsoft.com/try/app-service/mobile/)로 이동합니다. 여기서 App Service에 단기 시작 Mobile App을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기
다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 응용 프로그램에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 “할 일 목록” 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="configure-the-server-project"></a>서버 프로젝트 구성
아래 단계에 따라 Node.js 또는 .NET 백 엔드를 사용하도록 서버 프로젝트를 구성합니다.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms 솔루션 다운로드 및 실행
여기서 몇 가지를 선택합니다. Mac에 솔루션을 다운로드하고 Xamarin Studio에서 열 수 있습니다. 또는 iOS 앱을 빌드하는 네트워크에 연결된 Mac을 사용하여 Windows 컴퓨터에 솔루션을 다운로드하고 Visual Studio에서 열 수 있습니다. Xamarin 설정 시나리오에 대해 자세한 지침이 필요할 경우 [Visual Studio 및 Xamarin 설치 및 설정](https://msdn.microsoft.com/library/mt613162.aspx)을 참조하세요.

작업을 진행하겠습니다.

1. Mac 또는 Windows 컴퓨터의 브라우저 창에서 [Azure 포털] 을 엽니다.
2. 모바일 앱의 설정 블레이드에서 **시작**(모바일 아래) > **Xamarin.Forms**를 클릭합니다. 3단계 아래에서 **새 앱 만들기**가 선택되어 있지 않으면 클릭합니다.  그런 다음 **다운로드** 단추를 클릭합니다.
   
   모바일 앱에 연결된 클라이언트 응용 프로그램을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.
3. 다운로드한 프로젝트를 추출하고 Xamarin Studio 또는 Visual Studio에서 엽니다.
   
   ![][9]
   
   ![][8]

## <a name="optional-run-the-ios-project"></a>(선택 사항) iOS 프로젝트 실행
이 섹션에서는 iOS 장치용 Xamarin iOS 프로젝트를 실행합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-xamarin-studio"></a>Xamarin Studio에서
1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 메뉴에서 **디버깅 시작**을 클릭하여 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.

#### <a name="in-visual-studio"></a>Visual Studio에서
1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다.
3. **구성 관리자** 대화 상자에서 iOS 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
4. **F5** 키를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.
   
   > [!NOTE]
   > 빌드 문제가 있을 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. Quickstart 프로젝트는 최신 버전으로 업데이트되는 데 시간 지연이 발생할 수 있습니다.    
   > 
   > 

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 **+** 단추를 클릭합니다.

![][10]

Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

> [!NOTE]
> 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
> 
> 

## <a name="optional-run-the-android-project"></a>(선택 사항) Android 프로젝트 실행
이 섹션에서는 Android용 Xamarin Android 프로젝트를 실행합니다. Android 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-xamarin-studio"></a>Xamarin Studio에서
1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 메뉴에서 **디버깅 시작**을 클릭하여 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.

#### <a name="in-visual-studio"></a>Visual Studio에서
1. Android(Droid) 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다.
3. **구성 관리자** 대화 상자에서 Android 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
4. **F5** 키를 눌러 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작합니다.
   
   > [!NOTE]
   > 빌드 문제가 있을 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. Quickstart 프로젝트는 최신 버전으로 업데이트되는 데 시간 지연이 발생할 수 있습니다.    
   > 
   > 

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 **+** 단추를 클릭합니다.

![][11]

Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

> [!NOTE]
> 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
> 
> 

## <a name="optional-run-the-windows-project"></a>(선택 사항) Windows 프로젝트 실행
이 섹션에서는 Windows 장치용 Xamarin WinApp 프로젝트를 실행합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="in-visual-studio"></a>Visual Studio에서
1. Windows 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다.
3. **구성 관리자** 대화 상자에서 선택한 Windows 프로젝트의 **빌드** 및 **배포** 확인란을 선택합니다.
4. **F5** 키를 눌러 프로젝트를 빌드하고 Windows 에뮬레이터에서 앱을 시작합니다.
   
   > [!NOTE]
   > 빌드 문제가 있을 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 Xamarin 지원 패키지로 업데이트합니다. Quickstart 프로젝트는 최신 버전으로 업데이트되는 데 시간 지연이 발생할 수 있습니다.    
   > 
   > 

앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 **+** 단추를 클릭합니다.

Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

![][12]

> [!NOTE]
> 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs C# 파일에서 모바일 앱 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [앱에 인증 추가](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.
* [앱에 푸시 알림 추가](app-service-mobile-xamarin-forms-get-started-push.md)  
  앱에 푸시 알림 지원을 추가하고 모바일 앱 백 엔드를 구성하여 푸시 알림을 보내는 Azure 알림 허브를 사용하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.
* [Azure 모바일 앱에 관리되는 클라이언트를 사용하는 방법](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Xamarin 앱에서 관리되는 클라이언트 SDK로 작업하는 방법에 대해 알아봅니다. 

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 포털]: https://portal.azure.com/




<!--HONumber=Jan17_HO3-->


