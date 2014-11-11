<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# 모바일 서비스에서 오프라인 데이터 동기화 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a>
<a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ko-kr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서에서는 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 오프라인 지원을 추가하는 방법을 보여 줍니다. 이 방법을 통해 오프라인일 때 로컬 데이터베이스와 상호 작용할 수 있습니다. 또한 온라인으로 다시 전환되면 오프라인 기능을 사용하여 로컬 변경을 모바일 서비스와 동기화할 수 있습니다. 
</p>
<p>동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">오후 2:36:00</span></div>

</div>

이 자습서에서는 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서에서 작성한 앱을 업데이트하여 Azure 모바일 서비스의 오프라인 기능을 지원합니다. 그다음 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고, 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

> [WACOM.NOTE] 이 자습서는 Windows 스토어 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.
>
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [오프라인 기능을 지원하도록 앱 업데이트][오프라인 기능을 지원하도록 앱 업데이트]
2.  [오프라인 시나리오에서 앱 테스트][오프라인 시나리오에서 앱 테스트]
3.  [모바일 서비스를 다시 연결하도록 앱 업데이트][모바일 서비스를 다시 연결하도록 앱 업데이트]
4.  [모바일 서비스에 연결된 앱 테스트][모바일 서비스에 연결된 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

-   Windows 8.1에서 실행 중인 Visual Studio 2013
-   [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서 완료
-   [Azure 모바일 서비스 SDK 버전 1.3.0-beta2(이상)][Azure 모바일 서비스 SDK 버전 1.3.0-beta2(이상)]
-   [Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-beta2(이상)][Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-beta2(이상)]
-   SQLite for Windows 8.1

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

## <a name="enable-offline-app"></a>오프라인 기능을 지원하도록 앱 업데이트

Azure 모바일 서비스의 오프라인 기능을 사용하면 오프라인에서 모바일 서비스를 사용하여 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다.

이 섹션에서는 SQLite를 오프라인 기능에 대한 로컬 저장소로 사용합니다.

> [WACOM.NOTE] 이 섹션을 건너뛰고 오프라인 지원을 이미 제공하는 시작 프로젝트 버전을 바로 다운로드할 수 있습니다. 오프라인 지원이 설정된 프로젝트를 다운로드하려면 [시작 오프라인 샘플][시작 오프라인 샘플]을 참조하십시오.

1.  SQLite를 설치합니다. [SQLite for Windows 8.1][SQLite for Windows 8.1] 링크에서 SQLite를 설치할 수 있습니다.

    > [WACOM.NOTE] Internet Explorer를 사용하는 경우 SQLite를 설치하기 위해 링크를 클릭하면 .vsix를 .zip 파일로 다운로드할지를 묻는 메시지가 표시될 수 있습니다. 파일을 하드 드라이브의 원하는 위치에 .zip 대신 .vsix 확장명으로 저장합니다. Windows 탐색기에서 .vsix 파일을 두 번 클릭하여 설치를 실행합니다.

2.  Visual Studio에서 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서에서 완료한 프로젝트를 엽니다. 솔루션 탐색기의 프로젝트에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **Windows** \> **확장**에서 **Windows 런타임용 SQLite**에 대한 참조를 추가합니다.

    ![][0]

3.  SQLite 런타임을 사용하려면 빌드 중인 프로젝트의 프로세서 아키텍처를 **x86**, **x64** 또는 **ARM**으로 변경해야 합니다. **임의 CPU**는 지원되지 않습니다. 프로세서 아키텍처를 테스트하려는 지원되는 설정 중 하나로 변경합니다.

4.  Visual Studio의 솔루션 탐색기에서 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 실행합니다. **SQLiteStore**를 검색하여 **WindowsAzure.MobileServices.SQLiteStore** 패키지를 설치합니다.

    ![][1]

5.  Visual Studio의 솔루션 탐색기에서 MainPage.xaml.cs 파일을 엽니다. 파일 맨 위에 다음 using 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6.  Mainpage.xaml.cs에서 `todoTable`에 대한 선언을 `MobileServicesClient.GetSyncTable()`을 호출하여 초기화되는 `IMobileServicesSyncTable` 유형의 선언으로 바꿉니다.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7.  MainPage.xaml.cs에서 다음 **Version** 시스템 속성을 포함하도록 `TodoItem` 클래스를 업데이트합니다.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }

8.  MainPage.xaml.cs에서 `async` 메서드가 되고 SQLite 스토어에서 클라이언트 동기화 컨텍스트를 초기화하도록 `OnNavigatedTo` 이벤트 처리기를 업데이트합니다. SQLite 스토어는 모바일 서비스 테이블의 스키마와 일치하는 테이블로 작성되지만, 이전 단계에서 추가한 **Version** 시스템 속성을 포함해야 합니다.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9.  Visual Studio의 솔루션 탐색기에서 MainPage.xaml 파일을 엽니다. **Query and Update Data**라는 StackPanel을 포함하는 Grid 요소를 찾습니다. 행 정의에서 ListView의 시작 태그까지 요소를 바꾸는 다음 UI 코드를 추가합니다.

    이 코드는 표에 행 및 열 정의를 추가하여 요소를 배열합니다. 또한 **푸시** 및 **끌어오기** 작업에 대한 클릭 이벤트 처리기와 함께 두 단추 컨트롤을 추가합니다. 단추는 ListItems라는 `ListView` 바로 위에 배치됩니다. 파일을 저장합니다.

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <StackPanel Grid.Row="0" Grid.ColumnSpan="2">
            <local:QuickStartTask Number="2" Title="Query, Update, and Synchronize Data" 
              Description="Use the Pull and Push buttons to synchronize the local store with the server" />
        </StackPanel>
        <Button Grid.Row="1" Grid.Column="0" Margin="5,5,0,0" Name="ButtonPush"
            Click="ButtonPush_Click" Width="80" Height="34">Push</Button>
        <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
            Click="ButtonPull_Click" Width="80" Height="34">Pull</Button>
        <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">

10. MainPage.xaml.cs에서 **푸시** 및 **끌어오기** 단추에 대한 단추 클릭 이벤트 처리기를 추가하고 파일을 저장합니다.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync();
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageDialog d = new MessageDialog("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageDialog d = new MessageDialog(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
                await d.ShowAsync();
            }
        }

11. 아직 앱을 실행하지 마세요. **F7** 키를 눌러 프로젝트를 다시 빌드합니다. 빌드 오류가 발생하지 않는지 확인합니다.

## <a name="test-offline-app"></a>오프라인 시나리오에서 앱 테스트

이 섹션에서는 모바일 서비스와의 앱 연결을 끊고 오프라인 시나리오를 시뮬레이트합니다. 그런 다음 로컬 저장소에 저장할 일부 데이터 항목을 추가합니다.

이 섹션에서는 앱을 모바일 서비스에 연결하지 않습니다. 따라서 **푸시** 및 **끌어오기** 단추를 테스트할 때 예외가 발생합니다. 다음 섹션에서는 이 클라이언트 앱을 모바일 서비스에 다시 연결하여 저장소를 모바일 서비스 데이터베이스와 동기화하기 위한 **푸시** 및 **끌어오기** 작업을 테스트합니다.

1.  Visual Studio의 솔루션 탐색기에서 App.xaml.cs를 엽니다. URL에서 "**azure-mobile.net**"을 "**azure-mobile.xxx**"로 바꾸어 **MobileServiceClient** 초기화를 잘못된 주소로 변경합니다. 그런 다음 파일을 저장합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2.  Visual Studio에서 **F5** 키를 눌러 앱을 빌드하고 실행합니다. 새 todo 항목을 입력하고 **저장**을 클릭합니다. 새 todo 항목은 모바일 서비스에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 모든 만들기, 읽기, 업데이트, 삭제(CRUD) 작업을 지원하는 모바일 서비스에 연결된 것처럼 동작합니다.

    ![][2]

3.  앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

## <a name="update-online-app"></a>모바일 서비스를 다시 연결하도록 앱 업데이트

이 섹션에서는 앱을 모바일 서비스에 다시 연결합니다. 여기서는 모바일 서비스를 사용하여 오프라인 상태에서 온라인 상태로 전환되는 앱을 시뮬레이트합니다.

1.  Visual Studio의 솔루션 탐색기에서 App.xaml.cs를 엽니다. URL에서 "**azure-mobile.xxx**"를 "**azure-mobile.net**"으로 바꾸어 **MobileServiceClient** 초기화를 올바른 주소로 다시 변경합니다. 그런 다음 파일을 저장합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

## <a name="test-online-app"></a>모바일 서비스에 연결된 앱 테스트

이 섹션에서는 로컬 저장소를 모바일 서비스 데이터베이스와 동기화하기 위한 푸시 및 끌어오기 작업을 테스트합니다.

1.  Visual Studio에서 **F5** 키를 눌러 다시 빌드하고 실행합니다. 앱은 이제 모바일 서비스에 연결되지만 데이터는 오프라인 시나리오와 동일하게 보입니다. 이 앱은 로컬 저장소를 가리키는 `IMobileServiceSyncTable`로 항상 작업하기 때문입니다.

    ![][3]

2.  Microsoft Azure 관리 포털에 로그인하고 데이터베이스에서 모바일 서비스를 찾습니다. 서비스에서 모바일 서비스에 대해 JavaScript 백 엔드를 사용하는 경우 모바일 서비스의 **데이터** 탭에서 데이터를 찾아볼 수 있습니다. 모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 SQL Azure 확장 프로그램에서 데이터베이스의 **관리** 단추를 클릭하여 테이블에 대한 쿼리를 실행할 수 있습니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 않습니다.

    ![][4]

3.  앱에서 **푸시** 단추를 누릅니다. 그러면 앱이 `MobileServiceClient.SyncContext.PushAsync` 및 `RefreshTodoItems`를 차례로 호출하여 로컬 저장소에 있는 항목으로 앱을 새로 고칩니다. 이 푸시 작업은 모바일 서비스 데이터베이스에서 저장소의 데이터를 받습니다. 로컬 저장소에서는 모바일 서비스 데이터베이스의 항목을 받지 않습니다.

    푸시 작업은 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행되며 해당 동기화 컨텍스트에 연결된 모든 테이블에서 변경 사항을 푸시합니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다.

    ![][5]

4.  앱에서 로컬 저장소에 일부 새 항목을 추가합니다.

    ![][6]

5.  이번에는 앱에서 **끌어오기** 단추를 누릅니다. 앱은 `IMobileServiceSyncTable.PullAsync()`, `RefreshTodoItems`만 호출합니다. 모바일 서비스 데이터베이스의 모든 데이터를 로컬 저장소로 끌어온 다음 앱에 표시했습니다. 또한 로컬 저장소의 모든 데이터는 모바일 서비스 데이터베이스에 계속 푸시됩니다. **끌어오기 작업에서는 항상 푸시 작업을 먼저 수행하기** 때문입니다.

    ![][7]

    ![][8]

## 요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync`, `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

-   서버의 변경 내용을 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`를 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CRUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

-   서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`를 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다.

    또한 쿼리를 지정하도록 허용하는 **PullAsync()** 오버로드가 있습니다. 모바일 서비스에 대한 오프라인 지원 미리 보기 릴리스에서 **PullAsync**는 해당 테이블 또는 쿼리에서 모든 행을 읽습니다. 예를 들어 마지막 동기화보다 최신 상태인 행만 읽으려고 시도하지 않습니다. 행이 로컬 동기화 테이블에 이미 있는 경우 해당 행은 변경되지 않습니다.

## 다음 단계

-   [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리][모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started/
  [데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28
  [오프라인 기능을 지원하도록 앱 업데이트]: #enable-offline-app
  [오프라인 시나리오에서 앱 테스트]: #test-offline-app
  [모바일 서비스를 다시 연결하도록 앱 업데이트]: #update-online-app
  [모바일 서비스에 연결된 앱 테스트]: #test-online-app
  [시작 오프라인 샘플]: http://go.microsoft.com/fwlink/?LinkId=394777
  [SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
  [5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
  [6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
  [7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
  [8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
  [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/
