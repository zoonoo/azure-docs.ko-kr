<properties 
	pageTitle="기존 앱에 모바일 서비스 추가(Windows 스토어 JavaScript) | Microsoft Azure" 
	description="모바일 서비스를 사용하여 Windows 스토어 JavaScript 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>


# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##개요
이 항목에서는 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리 내에 저장하는 앱용 Visual Studio 2013 프로젝트를 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 변경된 데이터를 확인합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F)을 참조하십시오.
* Visual Studio 2013에서는 Windows 스토어 앱을 모바일 서비스에 쉽게 연결할 수 있도록 합니다.

##GetStartedWithData 프로젝트 다운로드

이 자습서는 Visual Studio 2013의 Windows 스토어 앱 프로젝트인 [GetStartedWithMobileServices 앱][Developer Code Samples site]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1. [개발자 코드 샘플 사이트](영문)에서 GetStartedWithData 샘플 앱의 JavaScript 버전을 다운로드합니다. 

2. Visual Studio에서 다운로드한 프로젝트를 열고 **js** 폴더를 확장한 후 default.js 파일을 검사합니다.

   	추가된 **TodoItem** 개체가 메모리 내 **List** 개체에 저장됩니다.

3. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4. 앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

   	![][0]

   	저장된 텍스트는 **데이터 쿼리 및 업데이트** 아래 두 번째 열에 표시됩니다.

##<a name="create-service"></a>Visual Studio에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>솔루션 탐색기에서 **services**, **mobile services**, **&lt;your_service>** 폴더를 확장하고 service.js 스크립트 파일을 연 후 새 전역 변수를 확인합니다. 다음 예제와 같이 표시됩니다.</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>이 코드는 전역 변수를 사용하여 앱에서 새 모바일 서비스에 액세스할 수 있게 합니다. 클라이언트는 새 모바일 서비스의 URI 및 응용 프로그램 키를 제공하여 만듭니다. 이 스크립트에 대한 참조가 default.html 파일에 추가되었으므로 이 페이지에서 참조된 모든 스크립트 파일도 이 변수를 사용할 수 있습니다.</p>
</li>
</ol>

##데이터 저장소로 사용할 새 테이블 추가

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##모바일 서비스를 사용하도록 앱 업데이트

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##새 모바일 서비스에 대해 앱 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2. 이전처럼 **Insert a TodoItem**에 텍스트를 입력하고 **Save**를 클릭합니다.

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.
  
   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

5. 앱에서 목록의 항목 중 하나를 선택하고 포털의 찾아보기 탭으로 돌아간 후 **새로 고침**을 클릭합니다.

  	완료 값이 **false**에서 **true**로 변경되었습니다.

6. default.js 프로젝트 파일에서 기존 **RefreshTodoItems** 함수를 완료된 항목을 필터링하는 다음 코드로 바꿉니다.

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. 앱에서 목록의 항목 중 다른 하나를 선택한 후 **새로 고침** 단추를 클릭합니다.

   	이제 선택된 항목이 목록에서 사라집니다. 새로 고침할 때마다 모바일 서비스로 왕복되며, 모바일 서비스는 필터링된 데이터를 반환합니다.

이제 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음으로 다른 항목 중 하나를 읽는 것이 좋습니다.

* [앱에 인증 추가](mobile-services-windows-store-javascript-get-started-users.md) <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 HTML/JavaScript 방법 개념 참조](mobile-services-html-how-to-use-client-library.md) <br/>HTML 및 JavaScript와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[개발자 코드 샘플 사이트]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=August15_HO7-->