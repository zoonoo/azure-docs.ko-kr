<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

정기 알림을 지원하는 사용자 지정 API 정의
=========================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/create-pull-notifications-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/create-pull-notifications-js "Windows 스토어 JavaScript")

이 항목에서는 Windows 스토어 앱에서 사용자 지정 API를 사용하여 정기 알림을 지원하는 방법을 보여 줍니다. 정기 알림을 사용하면 Windows에서 주기적으로 사용자 지정 API 끝점에 액세스하고 타일 특정 형식으로 반환된 XML을 사용하여 시작 메뉴의 앱 타일을 업데이트합니다. 자세한 내용은 [정기 알림](http://msdn.microsoft.com/en-us/library/windows/apps/jj150587.aspx)을 참조하십시오.

[모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started/#create-new-service) 또는 [데이터 시작](/en-us/develop/mobile/tutorials/started-with-data-js) 자습서를 마쳤을 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1.  [사용자 지정 API 정의](#define-custom-api)
2.  [정기 알림을 켜도록 앱 업데이트](#update-app)
3.  [앱 테스트](#test-app)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started/#create-new-service) 또는 [데이터 시작](/en-us/develop/mobile/tutorials/started-with-data-js)을 완료해야 합니다.

사용자 지정 API 정의
--------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][0]

2.  **API** 탭을 클릭한 후 **사용자 지정 API 만들기**를 클릭합니다.

   	![][1]

   	**Create a new custom API** 대화 상자가 표시됩니다.

3.  **사용 권한 얻기**를 **모든 사람**으로 변경하고 **API 이름**에 *tiles*를 입력한 후 확인 단추를 클릭합니다.

   	![][2]

    public GET 액세스 권한이 있는 새 API가 생성됩니다.

4.  API 테이블에서 새 tiles 항목을 클릭합니다.

    ![](./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png)

5.  **스크립트** 탭을 클릭하고 기존 코드를 다음으로 바꿉니다.

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

    이 코드는 TodoItem 테이블에서 완료되지 않은 상위 항목 3개를 반환한 후 **wns**.**createTileSquareText01** 함수에 전달되는 JSON 개체에 로드합니다. 이 함수는 다음과 같은 타일 템플릿 XML을 반환합니다.

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

 
   	<div class="dev-callout"><b>참고</b>
   		<p>이 사용자 지정 API 스크립트는 <strong>require</strong> 함수를 사용하여 참조되는 Node.js <a href="http://go.microsoft.com/fwlink/p/LinkId=306750">wns 모듈</a>을 사용합니다. 이 모듈은 서버 스크립트에서 푸시 알림을 보내는 데 사용되는 <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx">push 개체</a>에서 반환된 <a href="http://go.microsoft.com/fwlink/p/LinkId=260591">wns 개체</a>와 다릅니다.
   	</div>

다음에는 새 사용자 지정 API를 요청하여 라이브 타일을 업데이트하는 정기 알림을 시작하도록 빠른 시작 앱을 수정합니다.

앱 업데이트 정기 알림을 켜도록 앱 업데이트
------------------------------------------

1.  Visual Studio에서 F5 키를 눌러 이전 자습서의 빠른 시작 앱을 실행합니다.

2.  항목이 하나 이상 표시되는지 확인합니다. 항목이 없는 경우 **Insert a TodoItem**에 텍스트를 입력하고 **저장**을 클릭합니다.

3.  Visual Studio의 솔루션 탐색기에서 `\js` 폴더를 확장하고 default.js 프로젝트를 연 다음 해당 코드 뒤에 **client** 변수를 정의하는 다음 코드 줄을 추가합니다.

         var notifications = Windows.UI.Notifications;
         var recurrence = notifications.PeriodicUpdateRecurrence.hour;
         var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

         notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

    이 코드는 새 **tiles** 사용자 지정 API에서 타일 템플릿 데이터를 요청하기 위해 정기 알림을 켭니다. 데이터의 업데이트 빈도와 가장 일치하는 [PeriodicUpdateRecurrance] 값을 선택합니다.

앱 테스트
---------

1.  Visual Studio에서 F5 키를 눌러 앱을 다시 실행합니다.

    정기 알림이 켜집니다.

2.  시작 화면으로 이동하고 앱의 라이브 타일을 찾은 후 이제 타일에 항목 데이터가 표시되는 것을 확인합니다.

 	![](./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png)

다음 단계
---------

지금까지 정기 알림을 만들었습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보십시오.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-js)
    <br/>정기 알림은 Windows에서 관리되며 미리 정의된 일정에만 발생합니다. 푸시 알림은 모바일 서비스에서 주문형으로 전송할 수 있으며 알림 메시지, 타일 알림 및 원시 알림일 수 있습니다.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>사용자 지정 API 만들기에 대해 알아봅니다.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to turn on period notifications]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png
