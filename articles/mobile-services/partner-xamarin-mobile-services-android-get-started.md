<properties
	pageTitle="Xamarin Android용 모바일 서비스 시작 | Microsoft Azure"
	writer="craigd"
	description="Xamarin.Android 앱에서 Azure 모바일 서비스를 사용하는 방법에 대해 알아봅니다."
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/21/2016"
	ms.author="donnam"/>

# <a name="getting-started"></a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)] &nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [Xamarin.Android 앱 만들기](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md)를 참조하세요.

이 자습서에서는 Azure 모바일 서비스를 사용하여 Xamarin.Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다.

동영상을 시청하려는 경우 아래쪽의 클립은 이 자습서의 동일한 단계를 따릅니다.

비디오: "Xamarin 및 Azure 모바일 서비스 시작". 진행: Craig Dunn, Xamarin 개발자(시간: 10:05분)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 네트워크에 연결된 Mac과 함께 OS X용 XCode와 Xamarin Studio 또는 Windows의 Visual Studio가 필요합니다. 전체 설치 지침은 [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)에 있습니다.

다운로드한 빠른 시작 프로젝트에는 Xamarin.Android용 Azure 모바일 서비스 구성 요소가 포함되어 있습니다. 이 프로젝트는 Android 4.2 이상 버전을 대상으로 하지만 모바일 서비스 SDK에는 Android 2.2 이상 버전만 있으면 됩니다.

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5)을 참조하십시오.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 Xamarin.Android 앱 만들기

모바일 서비스를 만든 후 Azure 클래식 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 Xamarin.Android 앱을 만듭니다.

1.  [Azure 클래식 포털]에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 빠른 시작 탭의 **플랫폼 선택** 아래에서 **Xamarin.Android**를 클릭하고 **새 Android 앱 만들기**를 확장합니다.

	![][6]

	그러면 모바일 서비스에 연결된 Xamarin.Android 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

	![][7]

3. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

4. **앱 다운로드 및 실행**에서 **다운로드**를 클릭합니다.

	모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Android 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. 압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풉니다.

2. Xamarin Studio 또는 Visual Studio에서 **파일**, **열기**를 차례로 클릭하고 압축되지 않은 샘플 파일로 이동한 후 **XamarinTodoQuickStart.Android.sln**을 선택하여 엽니다.

3. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다. 에뮬레이터 또는 연결된 USB 장치를 선택하라는 메시지가 표시됩니다.

	> [AZURE.NOTE] Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.

4. 앱에서 _Complete the tutorial_ 등의 의미 있는 텍스트를 입력하고 **Add**를 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

	> [AZURE.NOTE]
   	모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.

6. [Azure 클래식 포털]에 돌아와서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

	![][11]

	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

	![][12]

## <a name="next-steps"> </a>다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* [오프라인 데이터 동기화 시작] 빠른 시작에서 오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 알아봅니다.

* [인증 시작] ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.



[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[오프라인 데이터 동기화 시작]: mobile-services-xamarin-android-get-started-offline-data.md
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-xamarin-android
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure]: http://azure.microsoft.com/
[Azure 클래식 포털]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->