<properties
	pageTitle="Xamarin Android 앱용 Azure 모바일 앱 시작 - Azure 모바일 앱"
	description="이 자습서에 따라 Azure 모바일 앱을 사용하여 Xamarin Android 개발을 시작할 수 있습니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"> </a>Xamarin.Android 앱 만들기

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

이 자습서에서는 Azure 모바일 앱을 사용하여 Xamarin Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 .NET 서비스와 .NET 백 엔드에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Xamarin Android 앱용 Azure 모바일 앱 자습서를 진행할 수 있습니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하십시오.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile)으로 이동합니다. 여기서 앱 서비스에 단기 시작 모바일 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 새 모바일 앱 백 엔드 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## 새 Xamarin Android 앱 만들기

모바일 앱 백 엔드를 만들었으면 [Azure 포털]에서 쉬운 빠른 시작을 따라 모바일 앱 백 엔드에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 자습서에서는 모바일 앱용 .NET 백 엔드 서비스 프로젝트와 새로운 Xamarin Android 앱을 다운로드합니다.

1. Azure 포털에서 **찾아보기**, **모바일 앱**을 차례로 클릭한 다음 방금 만든 모바일 앱을 클릭합니다.

2. 블레이드 맨 위에서 **클라이언트 추가**를 클릭하고 **Xamarin.Android**를 확장합니다.

    ![][6]

    모바일 앱 백 엔드에 연결된 Xamarin Android 앱을 만들기 위한 쉬운 3단계가 표시됩니다.


3. 아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>을 다운로드하여 설치합니다.

4. 아직 하지 않은 경우 [Xamarin Studio]를 다운로드하여 설치합니다. 또한 Visual Studio용 Xamarin을 사용할 수도 있습니다.

5. **서비스를 다운로드하고 클라우드에 게시** 아래에서 **다운로드**를 클릭합니다.

  	모바일 앱 백 엔드 코드 및 모바일 앱 백 엔드에 연결된 샘플 _할 일 모음_ 클라이언트 응용 프로그램 둘 다에 대한 프로젝트가 포함된 솔루션이 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

6. 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 앱 백 엔드 테스트

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## 모바일 앱 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Xamarin Android 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. Visual Studio 또는 Xamarin Studio에서 모바일 앱 솔루션 내의 클라이언트 프로젝트로 이동합니다.

	![][8]

	![][9]

2. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다. 에뮬레이터 또는 연결된 USB 장치를 선택하라는 메시지가 표시됩니다.

	> [AZURE.NOTE]Android 에뮬레이터에서 프로젝트를 실행할 수 있으려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.

3. 앱에서 _자습서 완료_ 등의 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 표시됩니다.

	> [AZURE.NOTE]모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure 포털]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=July15_HO3-->