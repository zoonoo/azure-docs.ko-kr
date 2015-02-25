<properties pageTitle="풀 알림을 지원하는 사용자 지정 API 정의 - Azure 모바일 서비스" description="Azure 모바일 서비스를 사용하는 Windows 스토어 앱에서 정기 알림을 지원하는 사용자 지정 API를 정의하는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>
# 정기 알림을 지원하는 사용자 지정 API 정의

<div class="dev-center-tutorial-selector"> 
	<a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications/" title="Windows Store C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications/" title="Windows Store JavaScript" class="current">Windows 스토어 JavaScript</a>
</div>

이 항목에서는 Windows 스토어 앱에서 사용자 지정 API를 사용하여 정기 알림을 지원하는 방법을 보여 줍니다. 정기 알림을 사용하면 Windows에서 주기적으로 사용자 지정 API 끝점에 액세스하고 타일 특정 형식으로 반환된 XML을 사용하여 시작 메뉴의 앱 타일을 업데이트합니다. 자세한 내용은 [정기 알림]을 참조하세요. 

[모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료할 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1. [사용자 지정 API 정의]
2. [정기 알림을 켜도록 앱 업데이트]
3. [앱 테스트] 

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작] 또는 [기존 앱에 모바일 서비스 추가] 자습서를 완료해야 합니다.  

## <a name="define-custom-api"></a>사용자 지정 API 정의

1. [Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][0]

2. **API** 탭을 클릭한 후 **사용자 지정 API 만들기**를 클릭합니다.

   	![][1]

   	**새 사용자 지정 API 만들기** 대화 상자가 표시됩니다.

3. **사용 권한 얻기**를 **모든 사람**으로 변경하고 **API 이름**에 _tiles_를 입력한 후 확인 단추를 클릭합니다.

   	![][2]

	public GET 액세스 권한이 있는 새 API가 생성됩니다.

4. API 테이블에서 새 tiles 항목을 클릭합니다.

	![][3]

5. **스크립트** 탭을 클릭하고 기존 코드를 다음으로 바꿉니다.

		exports.get = function(request, response) {
		    var wns = require('wns');
		    var todoItems = request.service.tables.getTable('TodoItem');
		    todoItems.where({
		        complete: false
		    }).read({
		        success: sendResponse
		    });
		
		    function sendResponse(results) {
		        var tileText = {
		            text1: "My todo list"
		        };
		        var i = 0;
		        console.log(results)
		        results.forEach(function(item) {
		            tileText["text" + (i + 2)] = item.text;
		            i++;
		        });
		        var xml = wns.createTileSquareText01(tileText);
		        response.set('content-type', 'application/xml');
		        response.send(200, xml);
		    }
		};

	이 코드는 TodoItem 테이블에서 완료되지 않은 상위 항목 3개를 반환한 후 **wns**.**createTileSquareText01** 함수에 전달되는 JSON 개체에 로드됩니다. 이 함수는 다음과 같은 타일 템플릿 XML을 반환합니다.

		<tile>
			<visual>
				<binding template="TileSquareText01">
					<text id="1">My todo list</text>
					<text id="2">Task 1</text>
					<text id="3">Task 2</text>
					<text id="4">Task 3</text>
				</binding>
			</visual>
		</tile>

	**exports.get** 함수는 클라이언트가 타일 템플릿에 액세스하는 GET 요청을 보내기 때문에 사용됩니다.

   	> [AZURE.NOTE] 이 사용자 지정 API 스크립트는 **require** 함수를 사용하여 참조되는 Node.js [wns 모듈](http://go.microsoft.com/fwlink/p/?LinkId=306750)을 사용합니다. 이 모듈은 서버 스크립트의 푸시 알림을 전송하는 데 사용되는, [푸시 개체](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj554217.aspx)에서 반환되는 [wns 개체](http://go.microsoft.com/fwlink/p/?LinkId=260591)와 다릅니다.

다음에는 새 사용자 지정 API를 요청하여 라이브 타일을 업데이트하는 정기 알림을 시작하도록 퀵 스타트 앱을 수정합니다.

<h2><a name="update-app"></a>정기 알림을 켜도록 앱 업데이트</h2>

1. Visual Studio에서 F5 키를 눌러 이전 자습서의 퀵 스타트 앱을 실행합니다.

2. 항목이 하나 이상 표시되는지 확인합니다. 항목이 없는 경우 **Insert a TodoItem**에 텍스트를 입력하고 **저장**을 클릭합니다.

3. Visual Studio의 솔루션 탐색기에서  `\js` 폴더를 확장하고 default.js 프로젝트를 연 다음 해당 코드 뒤에 **client** 변수를 정의하는 코드 줄을 추가합니다.

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	이 코드는 새 **tiles** 사용자 지정 API에서 타일 템플릿 데이터를 요청하기 위해 정기 알림을 켭니다. 데이터의 업데이트 빈도와 가장 일치하는 **PeriodicUpdateRecurrance** 값을 선택합니다.

## <a name="test-app"></a>앱 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 다시 실행합니다.

	정기 알림이 켜집니다.

2. 시작 화면으로 이동하고 앱의 라이브 타일을 찾은 후 이제 타일에 항목 데이터가 표시되는 것을 확인합니다.

 	![][4]

## 다음 단계

지금까지 정기 알림을 만들었습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보세요.

* [푸시 알림 시작]
	<br/>정기 알림은 Windows에서 관리되며 미리 정의된 일정에만 발생합니다. 푸시 알림은 모바일 서비스에서 주문형으로 전송할 수 있으며 알림 메시지, 타일 알림 및 원시 알림일 수 있습니다.

* [모바일 서비스 서버 스크립트 참조]
  <br/>사용자 지정 API 만들기에 대해 알아봅니다.

<!-- Anchors. -->
[사용자 지정 API 정의]: #define-custom-api
[정기 알림을 켜도록 앱 업데이트]: #update-app
[앱 테스트]: #test-app
[다음 단계]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Windows 푸시 알림 및 Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[내 응용 프로그램 대시보드]: http://go.microsoft.com/fwlink/?LinkId=262039
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[기존 앱에 모바일 서비스 추가](영문): /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push

[Azure 관리 포털]: https://manage.windowsazure.com/
[정기 알림]: http://msdn.microsoft.com/ko-kr/library/windows/apps/jj150587.aspx



<!--HONumber=42-->
