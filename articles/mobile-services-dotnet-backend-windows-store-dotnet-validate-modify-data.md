<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# .Net 백 엔드를 사용하여 모바일 서비스에서 데이터 유효성 검사 및 수정

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | 
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>

이 항목에서는 .Net 백 엔드 Azure 모바일 서비스에서 코드를 사용하여 데이터를 유효성 검사 및 수정하는 방법을 보여 줍니다. .Net 백 엔드 서비스는 Web API 프레임워크 및 Entity Framework에 기본 제공되는 HTTP 서비스입니다. Web API 프레임워크로 정의되는 `ApiController` 클래스에 대해 잘 알고 있다면 모바일 서비스에 제공되는 `TableController` 클래스는 쉽게 이해할 수 있습니다. `TableController`는 `ApiController` 클래스에서 파생되며 데이터베이스 테이블 연결을 위한 추가 기능을 제공합니다. 이 클래스는 이 자습서에서 다루는 유효성 검사 및 데이터 수정을 비롯하여 삽입 및 업데이트 중인 데이터에 대한 작업을 수행하는 데 사용될 수 있습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [문자열 길이 유효성 검사 추가][문자열 길이 유효성 검사 추가]
2.  [유효성 검사를 지원하도록 클라이언트 업데이트][유효성 검사를 지원하도록 클라이언트 업데이트]
3.  [길이 유효성 검사 테스트][길이 유효성 검사 테스트]
4.  [CompleteDate에 대한 타임스탬프 필드 추가][CompleteDate에 대한 타임스탬프 필드 추가]
5.  [CompleteDate를 표시하도록 클라이언트 업데이트][CompleteDate를 표시하도록 클라이언트 업데이트]

이 자습서는 이전 자습서인 [시작][시작] 또는 [데이터 시작][데이터 시작]의 단계 및 샘플 코드를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [시작][시작] 또는 [데이터 시작][데이터 시작] 자습서를 완료해야 합니다.

## <a name="string-length-validation"></a>유효성 검사 추가

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>클라이언트 업데이트

모바일 서비스가 데이터의 유효성 검사를 하고 잘못된 텍스트 길이에 대한 오류 응답을 보내도록 설정되어 있으므로, 개발자는 유효성 검사의 오류 응답을 처리할 수 있도록 앱을 업데이트해야 합니다. 오류는 `IMobileServiceTable<TodoItem].InsertAsync()`에 대한 클라이언트 앱 호출에서 `MobileServiceInvalidOperationException`으로 발생됩니다.

1.  Visual Studio의 솔루션 탐색기 창에서 클라이언트 프로젝트로 이동한 다음 MainPage.xaml.cs 파일을 엽니다. 이 파일에 다음 **using** 문을 추가합니다.

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2.  MainPage.xaml.cs에서 기존 **InsertTodoItem** 메서드를 다음 코드로 바꿉니다.

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    이 메서드 버전에는 메시지 대화 상자의 응답 콘텐츠에서 역직렬화 오류 메시지를 표시하는 **MobileServiceInvalidOperationException**에 대한 오류 처리가 포함됩니다.

## <a name="test-length-validation"></a>길이 유효성 검사 테스트

1.  Visual Studio의 솔루션 탐색기에서 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다. **F5** 키를 눌러 IIS Express에서 로컬로 서비스를 호스트하는 앱을 시작합니다.

2.  새 todo 항목에 길이가 10자를 초과하는 텍스트를 입력한 다음 **저장**을 클릭합니다.

    ![][0]

3.  잘못된 텍스트에 대한 응답으로 다음과 비슷한 메시지 대화 상자가 표시됩니다.

    ![][1]

## <a name="add-timestamp"></a>CompleteDate에 대한 타임스탬프 필드 추가

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>CompleteDate를 표시하도록 클라이언트 업데이트

마지막 단계는 새 **CompleteDate** 데이터를 표시하도록 클라이언트를 업데이트하는 것입니다.

1.  Visual Studio에서 솔루션 탐색기의 todolist 클라이언트 프로젝트에서 MainPage.xaml 파일을 열고 **CheckBoxComplete** 요소를 아래 정의로 바꿉니다. 그런 다음 파일을 저장합니다. **CheckBoxComplete**에 대한 이벤트 처리기가 `click` 이벤트를 처리하도록 변경됩니다. 또한 확인란 옆에 텍스트 블록을 추가하여 완료 날짜 타임스탬프에 바인딩합니다.

        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>

2.  Visual Studio에서 솔루션 탐색기의 todolist 클라이언트 프로젝트에서 MainPage.xaml.cs 파일을 열고 `CheckBoxComplete_Checked` 이벤트 처리기를 다음 `CheckBoxComplete_Clicked` 이벤트 처리기로 바꿉니다. 그러면 항목을 완료한 후 완료 날짜를 표시할 수 있습니다.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  MainPage.xaml.cs 파일에서 기존 **TodoItem** 클래스를 새 **CompleteDate** 속성을 nullable 유형으로 포함하는 다음 정의로 바꿉니다.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }

    > [WACOM.NOTE] `DataMemberAttribute`는 앱의 새 `CompleteDate` 속성을 TodoItem 테이블에 정의된 `CompleteDate` 열에 매핑하도록 클라이언트에 지시합니다. 이 특성을 사용하면 앱이 SQL 데이터베이스의 열 이름과 다른 개체 속성 이름을 가질 수 있습니다.

4.  MainPage.xaml.cs에서 완료된 todoitem이 결과에 포함되도록 기존 **RefreshTodoItems** 메서드에서 `.Where` 절 함수를 제거하거나 주석으로 처리합니다.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  MainPage.xaml.cs에서 업데이트 이후에 항목을 새로 고치고 완료된 항목을 목록에서 제거하지 않도록 **UpdateCheckedTodoItem** 메서드를 다음과 같이 업데이트합니다. 그런 다음 파일을 저장합니다.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  Visual Studio의 솔루션 탐색기 창에서 **솔루션**을 마우스 오른쪽 단추로 클릭하고 **솔루션 다시 빌드**를 클릭하여 클라이언트와 .NET 백 엔드 서비스를 모두 다시 빌드합니다. 두 프로젝트가 모두 오류 없이 빌드되는지 확인합니다.

    ![][2]

7.  **F5** 키를 눌러 클라이언트 앱 및 서비스를 로컬로 실행합니다. 새 항목을 추가하고 일부 항목을 완료 표시하여 업데이트되는 **CompleteDate** 타임스탬프를 확인합니다.

    ![][3]

8.  Visual Studio의 솔루션 탐색기에서 todolist 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. Azure 포털에서 다운로드한 게시 설정 파일을 사용하여 .NET 백 엔드 서비스를 Microsoft Azure에 게시합니다.

9.  모바일 서비스 주소에 대한 연결 주석 처리를 제거하여 클라이언트 프로젝트에 대한 App.xaml.cs 파일을 업데이트합니다. Azure 계정에 호스트된 .NET 백 엔드를 기준으로 앱을 테스트합니다.

## <a name="next-steps"> </a>다음 단계

이 자습서를 완료했으므로 이제 데이터 시리즈의 마지막 자습서인 [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]를 검토해 보십시오.

서버 스크립트는 사용자를 인증할 때 및 푸시 알림을 보내기 위해서도 사용됩니다. 자세한 내용은 다음 자습서를 참조하십시오.

-   [사용자의 서비스 쪽 권한 부여][사용자의 서비스 쪽 권한 부여]
    인증된 사용자의 ID를 기준으로 데이터를 필터링하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]
    .NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.

 
 


  [문자열 길이 유효성 검사 추가]: #string-length-validation
  [유효성 검사를 지원하도록 클라이언트 업데이트]: #update-client-validation
  [길이 유효성 검사 테스트]: #test-length-validation
  [CompleteDate에 대한 타임스탬프 필드 추가]: #add-timestamp
  [CompleteDate를 표시하도록 클라이언트 업데이트]: #update-client-timestamp
  [시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
  [사용자의 서비스 쪽 권한 부여]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library
