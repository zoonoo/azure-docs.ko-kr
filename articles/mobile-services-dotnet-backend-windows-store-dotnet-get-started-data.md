<properties linkid="mobile-services-dotnet-backend-windows-store-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# 모바일 서비스에서 데이터 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | 
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript 백 엔드">JavaScript 백 엔드</a>
</div>

이 항목에서는 Windows 스토어 앱용 백 엔드 데이터 소스로서 Azure 모바일 서비스를 사용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 2013 프로젝트를 다운로드하고, 새 모바일 서비스를 만들고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

이 자습서에서 만들 모바일 서비스는 .NET 백 엔드 모바일 서비스입니다. .NET 백 엔드를 활용하면 .NET 언어 및 Visual Studio를 모바일 서비스의 서버 쪽 비즈니스 논리에 사용할 수 있으며, 모바일 서비스를 로컬 컴퓨터에서 실행하고 디버깅할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 JavaScript 백 엔드 버전을 참조하세요.

> [WACOM.NOTE]이 항목에서는 Windows 스토어 프로젝트에 Azure 모바일 서비스를 추가하는 방법을 보여 줍니다. Visual Studio 2013 도구를 사용하여 동일한 .NET 백 엔드 모바일 서비스를 범용 Windows 앱 프로젝트에 추가할 수 있습니다. 자세한 내용은 이 자습서의 [범용 Windows 앱 버전][범용 Windows 앱 버전]을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Windows 스토어 앱 프로젝트 다운로드][Windows 스토어 앱 프로젝트 다운로드]
2.  [새 모바일 서비스 만들기][새 모바일 서비스 만들기]
3.  [로컬로 모바일 서비스 다운로드][로컬로 모바일 서비스 다운로드]
4.  [모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트][모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트]
5.  [로컬에 호스트한 서비스에 대해 Windows 스토어 앱 테스트][로컬에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]
6.  [Azure에 모바일 서비스 게시][Azure에 모바일 서비스 게시]
7.  [Azure에 호스트한 서비스에 대해 Windows 스토어 앱 테스트][Azure에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]

이 자습서를 완료하려면 다음이 필요합니다.

-   활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.
-   [Visual Studio 2013][Visual Studio 2013]. 무료 평가판을 이용할 수 있습니다.

## <a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 Visual Studio 2013의 Windows 스토어 앱 프로젝트인 [GetStartedWithMobileServices 앱][GetStartedWithMobileServices 앱](영문)을 기반으로 합니다. 이 앱의 UI는 추가된 항목이 로컬 메모리에 저장된다는 점을 제외하고 모바일 서비스 빠른 시작에서 생성된 앱과 유사합니다.

1.  [개발자 코드 샘플 사이트][GetStartedWithMobileServices 앱](영문)에서 GetStartedWithMobileServices 샘플 앱의 C# 버전을 다운로드합니다.

2.  Visual Studio를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭하여 Visual Studio 2013를 관리자 권한으로 실행합니다.

3.  Visual Studio 2013에서 다운로드한 프로젝트를 열고 MainPage.xaml.cs 파일을 검사합니다.

    추가된 **TodoItem** 개체가 메모리 내 **ObservableCollection\<TodoItem\>**에 저장됩니다.

4.  **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

5.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다.

    ![][0]

    저장된 텍스트가 **Query and update data** 아래 두 번째 열에 표시됩니다.

## <a name="create-service"></a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>모바일 서비스 프로젝트 다운로드 및 솔루션에 추가

1.  [Azure 관리 포털][Azure 관리 포털]에서 새 모바일 서비스 또는 해당 클라우드 아이콘 탭을 클릭하여 개요 페이지로 이동합니다.

    ![][1]

2.  **Windows 스토어** 플랫폼을 클릭합니다. **시작** 섹션 아래에서 **기존 Windows 스토어 앱 연결**을 클릭하고 **다운로드** 단추를 클릭하여 모바일 서비스의 개인화된 시작 프로젝트를 다운로드합니다.

    ![][2]

3.  **시작** 섹션의 아래쪽으로 스크롤하여 **클라우드에 서비스 게시**라는 단계로 이동합니다. 아래의 스크린샷에 보이는 링크를 클릭하여 방금 다운로드한 모바일 서비스용 게시 프로필 파일을 다운로드합니다.

    > [WACOM.NOTE] Azure 계정과 관련된 중요한 정보가 포함되어 있으므로 파일을 안전한 장소에 보관하세요. 이 자습서 뒤쪽에서 모바일 서비스를 게시한 후 이 파일을 삭제할 것입니다.

    ![][3]

4.  방금 다운로드한 개인화된 서비스 시작 프로젝트의 압축을 풉니다. 압축 파일에 있던 폴더를 Getting Started with Data 솔루션 파일(.sln)이 있는 **C#** 디렉터리로 복사합니다. 이렇게 하면 NuGet 패키지 관리자에서 모든 패키지의 동기화를 더욱 쉽게 유지할 수 있습니다.

    ![][4]

5.  그런 다음 Visual Studio의 솔루션 탐색기에서 Getting Started with Data Windows 스토어 앱용 솔루션을 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.

    ![][5]

6.  기존 프로젝트 추가 대화 상자에서, **C#** 디렉터리로 이동한 모바일 서비스 프로젝트 폴더를 찾습니다. 서비스 하위 디렉터리에서 C# 프로젝트 파일(.csproj)을 선택합니다. **열기**를 클릭하여 프로젝트를 솔루션에 추가합니다.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png)

7.  Visual Studio의 솔루션 탐색기에서, 방금 추가한 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 클릭하여 오류 없이 빌드되는지 확인합니다. 빌드 중에, 프로젝트에서 참조된 일부 NuGet 패키지를 NuGet 패키지 관리자에서 복원해야 할 수 있습니다.

    ![][7]

8.  서비스 프로젝트를 다시 마우스 오른쪽 단추로 클릭합니다. 이번에는 **디버그** 상황에 맞는 메뉴에서 **새 인스턴스 시작**을 클릭합니다.

    ![][8]

    Visual Studio에서 서비스용 기본 웹 페이지가 열립니다. 기본 웹 페이지에서 모바일 서비스의 메서드를 테스트하려면 **지금 시도**를 클릭할 수 있습니다.

    ![][9]

    Visual Studio에서는 기본적으로 IIS Express에 로컬로 모바일 서비스를 호스트합니다. 작업 표시줄에서 IIS Express용 트레이 아이콘을 마우스 오른쪽 단추로 클릭하여 확인할 수 있습니다.

    ![][10]

# <a name="update-app"></a>모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트

이 섹션에서는 모바일 서비스를 응용 프로그램용 백 엔드 서비스로 사용하기 위해 Windows 스토어 앱을 업데이트합니다.

1.  Visual Studio의 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

    ![][11]

2.  NuGet 패키지 관리 대화 상자의 온라인 패키지 컬렉션에서 **WindowsAzure.MobileServices**를 검색하고 클릭하여 Azure 모바일 서비스 Nuget 패키지를 설치합니다. 그런 다음 대화 상자를 닫습니다.

    ![][12]

3.  다시 Azure 관리 포털로 돌아와서 **앱 연결 및 서비스에 데이터 저장**이라는 레이블의 단계를 찾습니다. **C#** 언어가 선택되어 있는지 확인합니다. `MobileServiceClient` 연결을 만드는 코드 조각을 복사합니다.

    ![][13]

4.  Visual Studio에서 App.xaml.cs를 열고 `App` 클래스 정의의 시작 부분에 코드 조각을 붙여 넣습니다. 또한 다음 `using` 문을 이 파일 위에 추가하고 파일을 저장합니다.

        using Microsoft.WindowsAzure.MobileServices;

    ![][14]

5.  Visual Studio에서 MainPage.xaml.cs를 열고 using 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;

6.  Visual Studio의 MainPage.xaml.cs에서 `MainPage` 클래스 정의를 다음 정의와 교체하고 파일을 저장합니다.

    이 코드는 앱이 데이터를 로컬의 메모리 내 대신 서비스에서 제공하는 테이블에 저장하도록 모바일 서비스 SDK를 사용합니다. 세 가지 기본 메서드는 `InsertTodoItem`, `RefreshTodoItems` 및 `UpdateCheckedTodoItem`입니다. 이 세 가지 메서드를 사용하면 데이터 컬렉션을 Azure의 테이블에 비동기로 삽입하고 쿼리하며 업데이트할 수 있습니다.

        public sealed partial class MainPage : Page
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }

## <a name="test-locally-hosted"></a>로컬로 호스트한 서비스에 대해 Windows 스토어 앱 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>Azure에 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Azure에 게시된 모바일 서비스 테스트

1.  Visual Studio에서 App.xaml.cs를 엽니다. 로컬로 호스트한 모바일 서비스에 연결되는 `MobileServiceClient`를 만드는 코드를 주석으로 처리합니다. Azure의 서비스에 연결되는 `MobileServiceClient`를 만드는 코드의 주석 처리를 제거합니다. 파일 변경 사항을 저장합니다.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2.  Visual Studio에서 F5 키를 누르거나 디버그 메뉴에서 **디버깅 시작**을 클릭합니다. Azure에서 원격으로 호스트한 모바일 서비스에 연결하기 위해 앱을 실행하기 전에, 이전 변경 사항으로 Windows 스토어 앱이 다시 빌드됩니다.

3.  새 todoitem을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. 확인란을 클릭하여 몇몇 새 항목을 완료합니다. 각각의 새 todoItem이 저장되고, Azure 관리 포털에서 모바일 서비스에 대해 전에 구성한 SQL 데이터베이스에서 업데이트됩니다.

    ![][15]

    변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 다시 시작할 수 있습니다.

## <a name="view-stored-data"></a>SQL 데이터베이스에 저장된 데이터 보기

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

이제 **데이터 시작** 자습서를 마쳤습니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보세요.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정][스크립트를 사용하여 데이터 유효성 검사 및 수정]
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화][페이징을 사용하여 쿼리 구체화]
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다음 자습서 중 하나를 시도해 보십시오.

-   [인증 시작][인증 시작]
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

-   [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]
    .NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "JavaScript 백 엔드"
  [범용 Windows 앱 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
  [Windows 스토어 앱 프로젝트 다운로드]: #download-app
  [새 모바일 서비스 만들기]: #create-service
  [로컬로 모바일 서비스 다운로드]: #download-the-service-locally
  [모바일 서비스를 사용하도록 Windows 스토어 앱 업데이트]: #update-app
  [로컬에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]: #test-locally-hosted
  [Azure에 모바일 서비스 게시]: #publish-mobile-service
  [Azure에 호스트한 서비스에 대해 Windows 스토어 앱 테스트]: #test-azure-hosted
  [Azure 무료 평가판]: http://azure.microsoft.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [GetStartedWithMobileServices 앱]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png
  [5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
  [8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
  [스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
