<properties
	pageTitle="Windows 스토어 JavaScript 앱용 모바일 서비스 시작 | Azure 모바일 서비스"
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 JavaScript로 Windows 스토어 개발을 시작할 수 있습니다."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# 모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [Azure 모바일 앱으로 Windows 앱 만들기](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md)를 참조하세요.

이 자습서에서는 Azure 모바일 서비스를 사용하여 Windows 스토어 JavaScript 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)을 참조하세요.
* [Visual Studio 2013 Express for Windows]

## 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 Windows 스토어 앱 만들기

모바일 서비스를 만들고 나면 Azure 클래식 포털에서 쉬운 빠른 시작에 따라 모바일 서비스에 연결하는 새 Windows Store 8.1 JavaScript 앱을 만들 수 있습니다.

1.  [Azure 클래식 포털]에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.


2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **Windows**를 클릭하고 **새 Windows 스토어 앱 만들기**를 확장합니다.

3. 아직 수행하지 않은 경우 로컬 컴퓨터나 가상 컴퓨터에 [Visual Studio 2013][Visual Studio 2013 Express for Windows]을 다운로드하여 설치합니다.

4. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **앱 다운로드 및 실행**에서 앱의 언어를 선택하고 **다운로드**를 클릭합니다.

  	모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## Windows 앱 실행

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. 압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풀고 Visual Studio에서 솔루션 파일을 엽니다.

2. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

3. 앱에서 *Insert a TodoItem*에서 **Complete the tutorial** 등의 의미 있는 텍스트를 입력하고 **Save**를 클릭합니다.

   	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 모바일 서비스에서 반환되고 그 데이터가 앱의 두 번째 열에 표시됩니다.

4. (선택 사항) 다시 앱을 실행하고 앱이 시작된 후 이전 단계에서 저장한 데이터가 모바일 서비스에서 로드되는 것을 확인합니다.
 
4. [Azure 클래식 포털]에 돌아와서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

   	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

>[AZURE.NOTE] 모바일 서비스에 액세스하는 코드를 검토하여 데이터를 쿼리 및 삽입할 수 있습니다(default.js 파일에 있음).

## 다음 단계
이제 빠른 시작을 완료했으므로 [HTML/JavaScript용 모바일 서비스 클라이언트](mobile-services-html-how-to-use-client-library.md)로 작업하는 방법을 알아보세요.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure 클래식 포털]: https://manage.windowsazure.com/

<!----HONumber=AcomDC_0309_2016-->