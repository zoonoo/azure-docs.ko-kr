<properties 
	pageTitle="서버 스크립트를 사용하여 데이터 유효성 검사(Windows Phone) | 모바일 개발자 센터" 
	description="Windows Phone 앱에서 서버 스크립트를 사용하여 전송된 데이터의 유효성을 검사하고 데이터를 수정하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# 모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>이 항목에서는 Azure 모바일 서비스에서 서버 스크립트를 활용하는 방법을 보여 줍니다. 서버 스크립트는 모바일 서비스에서 등록되며 삽입 및 업데이트할 데이터에 대해 유효성 검사 및 데이터 수정을 포함한 다양한 작업을 수행하는 데 사용할 수 있습니다. 이 자습서에서는 데이터의 유효성을 검사하고 데이터를 수정하는 서버 스크립트를 정의하고 등록합니다. 서버 쪽 스크립트의 동작이 클라이언트에 영향을 미치는 경우가 많기 때문에 이런 새로운 동작을 활용하도록 Windows Phone 8 앱도 업데이트합니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a> <span class="time">오전 11:36</span></div>
</div>

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [문자열 길이 유효성 검사 추가]
2. [유효성 검사를 지원하도록 클라이언트 업데이트]
3. [삽입 시 타임스탬프 추가]
4. [타임스탬프를 표시하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [기존 앱에 모바일 서비스 추가](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-data/)의 단계와 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 이 자습서를 완료해야 합니다.  

## <a name="string-length-validation"></a>유효성 검사 추가

사용자가 제출하는 데이터 길이의 유효성을 항상 검사하는 것이 좋습니다. 먼저, 모바일 서비스에 전송되는 문자열 데이터 길이의 유효성을 검사하고 너무 긴 경우(이 예제에서는 10자 초과) 문자열을 거부하는 스크립트를 등록합니다.

1. [Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다. 

	![][0]

2. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![][1]

3. **스크립트**를 클릭한 후 **삽입** 작업을 선택합니다.

	![][2]

4. 기존 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    이 스크립트는 **TodoItem.text** 속성의 길이를 확인하여 길이가 10자를 초과하는 경우 오류 응답을 보냅니다. 그렇지 않으면 **execute** 메서드가 호출되어 삽입이 완료됩니다.

    > [AZURE.TIP] **지우기**를 클릭한 후 **저장**을 클릭하여 **스크립트** 탭에서 등록된 스크립트를 제거할 수 있습니다.	

## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 오류 응답을 보내므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다.

1. Visual Studio 2012 Express for Windows Phone에서 [데이터 시작 자습서]를 완료할 때 수정한 프로젝트를 엽니다.

2. **F5** 키를 눌러 앱을 실행한 후 텍스트 상자에 10자가 넘는 텍스트를 입력하고 **저장**을 클릭합니다.

   	앱은 모바일 서비스에서 반환된 400 응답(잘못된 요청)의 결과로 처리되지 않은 **MobileServiceInvalidOperationException**을 생성합니다.

6. 	MainPage.xaml.cs 파일을 연 다음 기존 **InsertTodoItem** 메서드를 다음과 같이 바꿉니다.

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
			// When the operation completes and Mobile Services has 
			// assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
					MessageBoxButton.OK);
            }
        }

   	이 메서드 버전에는 MessageBox에 오류 응답을 표시하는 **MobileServiceInvalidOperationException**에 대한 오류 처리가 포함됩니다.

## <a name="add-timestamp"></a>타임스탬프 추가

이전 작업에서는 삽입의 유효성을 검사하여 삽입을 허용하거나 거부했습니다. 이제, 삽입되기 전에 개체에 타임스탬프 속성을 추가하는 서버 스크립트를 사용하여 삽입된 데이터를 업데이트합니다.

> [AZURE.NOTE] 여기 나온 **createdAt** 타임스탬프 속성은 이제 중복됩니다. 모바일 서비스는 각 테이블의 **__createdAt** 시스템 속성을 자동으로 만듭니다. TodoItem 클래스에 다음 멤버를 추가하기만 하면 이 시스템 속성을 응용 프로그램에서 사용할 수 있습니다.  
> 
`````
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
`````


1. [관리 포털]의 **스크립트** 탭에서 현재 **삽입** 스크립트를 다음 함수로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    이 함수는 **request**.**execute** 호출에 의해 삽입되기 전에 개체에 새 **createdAt** 타임스탬프 속성을 추가하여 이전 삽입 스크립트를 보강합니다. 

    > [AZURE.IMPORTANT] 이 삽입 스크립트를 처음 실행할 때는 동적 스키마를 사용하도록 설정해야 합니다. 동적 스키마를 사용하도록 설정하면 첫 번째 실행 시 모바일 서비스가 **createdAt** 열을 **TodoItem** 테이블에 자동으로 추가합니다. 동적 스키마는 기본적으로 새 모바일 서비스에 대해 사용하도록 설정되어 있으며 Windows Phone 스토어에 앱을 게시하기 전에 이 스키마를 사용하지 않도록 설정해야 합니다.

2. Visual Studio에서 **F5** 키를 눌러 앱을 실행한 후 텍스트 상자에 10자 미만의 텍스트를 입력하고 **저장**을 클릭합니다.

   	새 타임스탬프는 앱 UI에서 표시되지 않습니다.

3. 관리 포털로 돌아가서 **todoitem** 테이블에서 **찾아보기** 탭을 클릭합니다.
   
   	이제 **createdAt** 열이 생겼고 새로 삽입된 항목에 타임스탬프 값이 추가되었습니다.
  
다음으로, 이 새 열을 표시하도록 Windows Phone 앱을 업데이트해야 합니다.

## <a name="update-client-timestamp"></a>클라이언트 다시 업데이트

모바일 서비스 클라이언트는 정의 형식에 대한 속성으로 직렬화할 수 없는 응답 데이터를 무시합니다. 마지막 단계는 이 새 데이터를 표시하도록 클라이언트를 업데이트하는 것입니다.

1. Visual Studio에서 MainPage.xaml.cs 파일을 연 후 기존 **TodoItem** 클래스를 다음 정의로 바꿉니다.

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }
	
    이 새로운 클래스 정의에는 새 타임스탬프 속성이 nullable DateTime 형식으로 포함되어 있습니다.
  
    > [AZURE.NOTE] **DataMemberAttribute**는 앱의 새 **CreatedAt** 속성을 TodoItem 테이블에 정의된 **createdAt** 열(대/소문자가 다름)에 매핑하도록 클라이언트에 지시합니다. 이 특성을 사용하면 앱이 SQL 데이터베이스의 열 이름과 다른 개체 속성 이름을 가질 수 있습니다. 이 특성을 사용하지 않는 경우 대/소문자 차이 때문에 오류가 발생합니다.

5. MainPage.xaml 파일의 **CheckBoxComplete** 요소 바로 아래에 다음 XAML 요소를 추가합니다.
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	텍스트 상자에 새 **CreatedAt** 속성이 표시됩니다. 
	
6. **F5** 키를 눌러 앱을 실행합니다. 

   	타임스탬프는 삽입 스크립트를 업데이트한 후 삽입된 항목에만 표시됩니다.

7. 기존 **RefreshTodoItems** 메서드를 다음 코드로 바꿉니다.

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListIte
	ms.ItemsSource = items;
        }

   	또한 이 메서드는 쿼리를 업데이트하여 타임스탬프 값이 없는 항목을 필터링합니다.
	
8. **F5** 키를 눌러 앱을 실행합니다.

   	타임스탬프 값 없이 생성된 모든 항목이 UI에서 사라집니다.

데이터 작업에 대한 이 자습서를 완료했습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 [페이징을 사용하여 쿼리 구체화] 자습서를 참조하세요.

* [스크립트를 통해 사용자 권한 부여]
  <br/>인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 서버 스크립트 참조]
  <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아봅니다.

<!-- Anchors. -->
[문자열 길이 유효성 검사 추가]: #string-length-validation
[유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
[삽입 시 타임스탬프 추가]: #add-timestamp
[타임스탬프를 표시하도록 클라이언트 업데이트]: #update-client-timestamp
[다음 단계]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-wp8
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-wp8
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-wp8
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-wp8
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-wp8

[관리 포털]: https://manage.windowsazure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/



<!--HONumber=42-->
