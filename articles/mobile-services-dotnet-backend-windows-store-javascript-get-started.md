<properties pageTitle="Windows 스토어 앱에 대한 모바일 서비스 시작 | 모바일 개발자 센터" description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 C#, VB 또는 JavaScript로 Windows 스토어 개발을 시작할 수 있습니다." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>


# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서에서는 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 HTML 및 JavaScript의 간단한  *To do list* 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 JavaScript 버전을 참조하세요.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)을 참조하세요.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. 무료 평가판을 이용할 수 있습니다.


## 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 새 범용 Windows 앱 만들기

모바일 서비스를 만든 후 관리 포털에서 쉬운 퀵 스타트를 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다. 

이 섹션에서는 모바일 서비스에 연결된 새 범용 Windows 앱을 만듭니다.

1. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.
   
2. 퀵 스타트 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

   	![][6]

   	모바일 서비스에 연결된 Windows 스토어 앱을 만드는 간편한 세 가지 단계가 표시됩니다.

  	![][7]

3. 아직 하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>을 다운로드하여 설치합니다.

4. **앱 및 서비스를 로컬로 다운로드 및 실행**에서 Windows 스토어 앱의 언어를 선택하고 **다운로드**를 클릭합니다. 

  	모바일 서비스 및 여기에 연결된 샘플 _할 일 모음_ 응용 프로그램 모두를 위한 프로젝트가 포함된 솔루션이 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.


## 로컬 모바일 서비스에 대해 앱 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(default.js 파일에 있음).

## 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>공유 코드 프로젝트에서 default.js 파일을 열어 <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a> 인스턴스를 만드는 코드를 찾은 다음, <em>localhost</em>를 사용하여 이 클라이언트를 만드는 코드는 주석 처리하고 원격 모바일 서비스 URL을 사용하여 클라이언트를 만드는 코드는 주석 처리를 제거합니다. 결과는 다음과 같습니다.</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>이제 클라이언트가 Azure에 게시된 모바일 서비스에 액세스할 수 있습니다.</p></li>

<li><p><strong>F5</strong> 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.</p></li>

<li><p>앱에서 <strong>Insert a TodoItem</strong>에 <em>자습서 완료</em>와 같은 의미 있는 텍스트를 입력하고 <strong>저장</strong>을 클릭합니다.</p>

<p>Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다.</p>
</li>
<li><p>(선택 사항) 범용 Windows 솔루션에서 기본 시작 프로젝트를 다른 앱으로 변경하고 <strong>F5</strong> 키를 다시 누릅니다.</p>

	<p>앱이 시작된 후 이전 단계에서 저장한 데이터가 모바일 서비스에서 로드됩니다.</p></li>
</ol>


## 다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다. 

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작](영문)
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작]
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

범용 Windows 앱에 대한 자세한 내용은 [단일 모바일 서비스에서 여러 장치 플랫폼 지원](/ko-kr/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).을 참조하세요.

<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[모바일 서비스 인스턴스 정의]:#define-mobile-service-instance
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[모바일 서비스 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript 및 HTML]: mobile-services-win8-javascript/
[관리 포털]: https://manage.windowsazure.com/
[JavaScript 버전]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started
[Visual Studio 2012를 사용하여 모바일 서비스에서 데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012


<!--HONumber=42-->
