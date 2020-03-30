---
title: Xamarin.Android 앱 시작하기
description: 이 자습서를 따라 Xamarin Android 개발을 위한 Azure 모바일 앱 사용을 시작하십시오.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461371"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Xamarin Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [Mobile Apps 정의](app-service-mobile-value-prop.md)를 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Xamarin Android 앱용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 활성 Azure 계정. 계정이 아직 없으면 Azure 평가판에 등록하고 최대 10개의 무료 Mobile Apps을 가져옵니다. 자세한 내용은 [Azure 무료 평가판을](https://azure.microsoft.com/pricing/free-trial/)참조하십시오.
* Xamarin이 포함된 Visual Studio입니다. 지침은 [Visual Studio 및 Xamarin을 위한 설치 및 설정](/visualstudio/cross-platform/setup-and-install) 을 참조하세요.

## <a name="create-an-azure-mobile-app-backend"></a>새 Azure Mobile App 백 엔드 만들기
다음 단계에 따라 새 Mobile App 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 애플리케이션에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 "할 일 목록" 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결 만들기 및 클라이언트 및 서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>자마린.안드로이드 응용 프로그램을 실행
1. Xamarin.Android 프로젝트를 엽니다.

2. [Azure 포털로](https://portal.azure.com/) 이동하여 만든 모바일 앱으로 이동합니다. 블레이드에서 `Overview` 모바일 앱의 공개 끝점인 URL을 찾습니다. 예 - 내 앱 이름 "test123"의 https://test123.azurewebsites.net사이트 이름은 .

3. 이 폴더에서 파일을 `ToDoActivity.cs` 엽니 다 - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. 응용 프로그램 `ZUMOAPPNAME`이름은 입니다.

4. 클래스에서는 `ToDoActivity` 변수를 위의 공용 끝점으로 바꿉습니다. `ZUMOAPPURL`

    `const string applicationURL = @"ZUMOAPPURL";`

    다음이 됩니다.
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. F5 키를 눌러 앱을 배포하고 실행합니다.

6. 앱에서 *자습서 완료* 등의 의미 있는 텍스트를 입력한 후 **추가** 단추를 클릭합니다.

    ![][10]

    요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 나타납니다.

   > [!NOTE]
   > 모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.
   
## <a name="troubleshooting"></a>문제 해결
솔루션을 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 `Xamarin.Android` 지원 패키지를 업데이트합니다. 빠른 시작 프로젝트는 항상 최신 버전을 포함하지 않을 수 있습니다.

프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
