<properties pageTitle="데이터 시작(HTML 5) | 모바일 개발자 센터" description="모바일 서비스를 사용하여 HTML 앱에서 데이터를 활용하는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>

# 기존 앱에 모바일 서비스 추가(영문)

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 HTML 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 변경된 데이터를 확인합니다.

>[AZURE.NOTE]이 자습서는 HTML 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 여러 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 <a href="/ko-kr/develop/mobile/tutorials/get-started-html">모바일 서비스 시작</a> 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [HTML 앱 프로젝트 다운로드]
2. [모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F"%20target="_blank)을 참조하세요.

###추가 요구 사항

모든 웹 서버에서 GetStartedWithData 앱을 호스트할 수 있습니다. 하지만 편의를 위해 `http://localhost:8000`에서 앱을 실행할 수 있게 하는 스크립트가 제공되었습니다.
 
+ 이 자습서에서 localhost를 사용하려면 로컬 컴퓨터에 다음 웹 서버 중 하나가 실행되고 있어야 합니다.

	+  **Windows**: IIS Express. IIS Express는 [Microsoft 웹 플랫폼 설치 관리자]를 통해 설치됩니다.   
	+  **MacOS X**: Python이 미리 설치되어 있어야 합니다.
	+  **Linux**: Python. [Python의 최신 버전]을 설치해야 합니다. 
	
	앱을 호스트하기 위한 웹 서버로 어떤 것이든 사용할 수 있지만, 다운로드한 스크립트는 위의 웹 서버에서 만 지원됩니다.  

+ HTML5를 지원하는 웹 브라우저.

<h2><a name="download-app"></a>GetStartedWithData 프로젝트 다운로드</h2>

이 자습서는 HTML5 앱인 [GetStartedWithData 앱]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 퀵 스타트에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다. 

1. [][GetStartedWithData 앱 프로젝트 파일을 다운로드합니다].

2. HTML 편집기에서 다운로드된 프로젝트를 열고 app.js 파일을 검사합니다.

   	추가한 항목은 메모리 내 **Array** 개체에 저장됩니다(**staticItems**). 페이지를 새로 고치면 데이터가 사라집니다. 이 데이터는 지속되지 않습니다.

3. **server** 하위 폴더에서 다음 명령 파일 중 하나를 시작합니다.

	+ **launch-windows**(Windows 컴퓨터)
	+ **launch-mac.command**(Mac OS X 컴퓨터)
	+ **launch-linux.sh**(Linux 컴퓨터)

	> [AZURE.NOTE] Windows 컴퓨터의 경우 PowerShell에서 스크립트를 실행할 것인지 물으면 'R'을 입력하세요. 인터넷에서 다운로드한 스크립트이므로 웹 브라우저에서 스크립트를 실행하지 말라는 경고가 나타날 수 있습니다. 이 경우 브라우저에서 스크립트 로드를 계속 진행하도록 요청해야 합니다.
	
	새로운 앱을 호스트할 수 있도록 로컬 컴퓨터에서 웹 서버가 시작됩니다.

4. 웹 브라우저에서 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>을 열어 앱을 시작합니다.

5. **새 작업 입력**에서 _Complete the tutorial_와 같은 의미 있는 텍스트를 입력한 후 **추가**를 클릭합니다.

   	![][0]  

   	저장된 텍스트는 **staticItems** 배열에 추가되며, 페이지를 새로 고쳐 새 항목이 표시됩니다.

<h2><a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>모바일 서비스에 새 테이블 추가</h2>

새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 연결된 SQL 데이터베이스 인스턴스에 새 테이블을 만들어야 합니다.

1. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

   	![][5]

   	**새 테이블 만들기** 대화 상자가 표시됩니다.

3. **테이블 이름**에 _TodoItem_을 입력한 후 확인 단추를 클릭합니다.

  	![][6]

  	기본 사용 권한이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 이는 앱과 함께 배포되는 응용 프로그램 키가 있다면 누구나 테이블의 데이터에 액세스하고 내용을 변경할 수 있다는 것을 의미합니다.

    > [AZURE.NOTE] 동일한 테이블 이름이 모바일 서비스 퀵 스타트에서 사용됩니다. 하지만 각 테이블은 주어진 모바일 서비스에 한정된 스키마에 생성됩니다. 따라서 여러 모바일 서비스가 같은 데이터베이스를 사용할 때 발생하는 데이터 충돌이 방지됩니다.

4. 새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

5. **열** 탭을 클릭합니다. 다음의 기본 열이 자동으로 생성되었는지 확인합니다. 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>열 이름</th>
 	<th>유형</th>
 	<th>인덱스</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	이는 모바일 서비스의 테이블에 대한 최소 요구 사항입니다. 

    > [AZURE.NOTE] 모바일 서비스에서 동적 스키마를 사용하도록 설정하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.

6. **구성** 탭의 **CORS(크로스-원본 자원 공유)**(영문) 아래에 있는 **호스트 이름의 요청 허용** 목록에  `localhost`가 이미 나열되어 있는지 확인합니다. 그렇지 않으면 **호스트 이름** 필드에  `localhost`를 입력하고 **저장**을 클릭합니다.

  	![][11]

	> [AZURE.IMPORTANT] localhost 이외의 웹 서버에 퀵 스타트 앱을 배포하는 경우, 웹 서버 호스트 이름을 **호스트 이름의 요청 허용** 목록에 추가해야 합니다. 자세한 내용은 [CORS(크로스-원본 자원 공유)](http://msdn.microsoft.com/ko-kr/library/windowsazure/dn155871.aspx"%20target="_blank)(영문)를 참조하세요.

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.

<h2><a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트</h2>

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다. 

3. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

4. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록한 후 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록합니다.

   	![][8]

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

1. 웹 편집기에서 index.html 프로젝트 파일을 열고 페이지의 스크립트 참조에 다음 코드를 추가합니다.

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. 편집기에서 app.js 파일을 열고 **MobileServiceClient** 변수를 정의하는 다음 코드에 달린 주석을 제거하고 **MobileServiceClient** 생성자에 모바일 서비스의 URL 및 응용 프로그램 키를 이 순서대로 제공합니다.

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	모바일 서비스에 액세스하는 데 사용되는 새 MobileServiceClient 인스턴스가 만들어집니다.

6. 다음 코드 줄을 주석으로 처리합니다.

		var itemCount = 0;
		var staticItems = [];

	데이터가 메모리 내 배열이 아니라 모바일 서비스에 저장됩니다.

6. 다음 코드 줄의 주석 처리를 제거합니다.

        todoItemTable = client.getTable('todoitem');

   	이 코드는 SQL 데이터베이스 **TodoItem**의 프록시 개체(**todoItemTable**)를 만듭니다. 

7. **$('#add-item').submit** 이벤트 처리기를 다음 코드로 바꿉니다.

		$('#add-item').submit(function(evt) {
			var textbox = $('#new-item-text'),
				itemText = textbox.val();
			if (itemText !== '') {
				todoItemTable.insert({ text: itemText, complete: false })
					.then(refreshTodoItems);
			}
			textbox.val('').focus();
			evt.preventDefault();
		});


  	이 코드는 테이블에 새 항목을 삽입합니다.

8. **refreshTodoItems** 메서드를 다음 코드로 바꿉니다.

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
				listItems = $.map(todoItems, function(item) {
					return $('<li>')
						.attr('data-todoitem-id', item.id)
						.append($('<button class="item-delete">Delete</button>'))
						.append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
						.append($('<div>').append($('<input class="item-text">').val(item.text)));
				});
					   
				$('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
				$('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
			});
		}
	   

   모든 항목을 반환하는 쿼리가 모바일 서비스에 전송됩니다. 결과가 반복되며, 데이터가 페이지에 표시됩니다. 

9. **$(document.body).on('change', '.item-text')** 및 **$(document.body).on('change', '.item-complete')** 이벤트 처리기를 다음 코드로 바꿉니다.
        
		$(document.body).on('change', '.item-text', function() {
			var newText = $(this).val();
			todoItemTable.update({ id: getTodoItemId(this), text: newText });
		});

		$(document.body).on('change', '.item-complete', function() {
			var isComplete = $(this).prop('checked');
			todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
				.then(refreshTodoItems);
		});
 
   	텍스트가 변경되거나 상자가 선택될 때 모바일 서비스에 항목 업데이트가 전송됩니다.

10. **$(document.body).on('click', '.item-delete')** 이벤트 처리기를 다음 코드로 바꿉니다.

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	**삭제** 단추가 클릭되면 모바일 서비스에 삭제가 전송됩니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

<h2><a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트</h2>

4. 웹 브라우저에서 URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a>을 다시 로드하여 앱을 시작합니다.

    > [AZURE.NOTE] 웹 서버를 다시 시작해야 하는 경우 첫 번째 섹션의 단계를 반복합니다.

2. 앞서와 같이 **Enter new task(새 작업 입력)**에 텍스트를 입력한 후 **추가**를 클릭합니다. 

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]
  
   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

5. 앱에서 목록의 항목 중 하나를 선택하고 포털의 찾아보기 탭으로 돌아간 후 **새로 고침**을 클릭합니다. 

  	완료 값이 **false**에서 **true**로 변경되었습니다.

6. app.js 프로젝트 파일에서 **RefreshTodoItems** 메서드를 찾아  `query`를 정의하는 코드 줄을 다음으로 바꿉니다.

   		var query = todoItemTable.where({ complete: false });

7. 페이지를 다시 로드하고 목록에서 다른 항목 하나를 확인합니다.

   	이제 선택된 항목이 목록에서 사라집니다. 각 업데이트 결과로 모바일 서비스 왕복이 발생하며, 이에 따라 필터링된 데이터가 반환됩니다.

이제 **데이터 시작 자습서**를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 HTML 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.
 
데이터 계열을 완료했습니다. 이제 앱 사용자를 인증하는 방법을 알아보고, [인증 시작](영문)을 완료하여 다른 자습서도 학습해 보세요.

<!-- Anchors. -->
[HTML 앱 프로젝트 다운로드]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-html
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-html
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-html

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[GetStartedWithData 앱]:  http://go.microsoft.com/fwlink/?LinkID=286345

[모바일 서비스 HTML/JavaScript 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-html-js-client

[크로스-원본 자원 공유]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn155871.aspx



<!--HONumber=42-->
