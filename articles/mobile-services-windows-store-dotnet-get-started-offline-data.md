<properties pageTitle="모바일 서비스에서 오프라인 데이터 사용(Windows 스토어) | 모바일 개발자 센터" description="Azure 모바일 서비스를 사용하여 Windows 스토어 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="12/10/2014" ms.author="wesmc"/>

# 모바일 서비스에서 오프라인 데이터 동기화 사용

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서에서는 Azure 모바일 서비스를 사용하여 범용 Windows 스토어 앱에 오프라인 지원을 추가하는 방법을 보여 줍니다. 오프라인 지원을 사용하면 오프라인 시나리오의 앱에서 로컬 데이터베이스를 조작할 수 있습니다. 앱이 백 엔드 데이터베이스와 함께 온라인 상태가 되면 오프라인 기능을 사용하여 로컬 변경 내용을 동기화합니다. 
</p>
<p>동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">비디오 재생</span></a> <span class="time">오후 2:36</span></div>
</div>


이 자습서에서는 Azure 모바일 서비스의 오프라인 기능을 지원하도록 [모바일 서비스 시작](영문)의 범용 앱 프로젝트를 업데이트합니다. 그런 다음 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고, 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.


>[AZURE.NOTE] 이 자습서는 Windows 스토어 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.
>
>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요. 
>
>Visual Studio 2012용 이전 Windows Phone 8 자습서도 [Visual Studio 2012용 Windows Phone 8 자습서]에서 계속 사용할 수 있습니다.


이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [오프라인 기능을 지원하도록 앱 업데이트]
2. [앱의 동기화 동작 업데이트]
3. [모바일 서비스를 다시 연결하도록 앱 업데이트]

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* [모바일 서비스 시작 ](영문) 완료
* [Azure 모바일 서비스 SDK 1.3.0 이상 버전][모바일 서비스 SDK Nuget]
* [Azure 모바일 서비스 SQLite 저장소 1.0.0 이상 버전][SQLite 저장소 Nuget]
* [SQLite for Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요. 

## <a name="enable-offline-app"></a>오프라인 기능을 지원하도록 앱 업데이트

Azure 모바일 서비스의 오프라인 기능을 사용하면 오프라인 시나리오에서 모바일 서비스를 사용하여 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서  `MobileServiceClient.SyncContext`를 초기화합니다. 그런 다음  `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다. 이 자습서에서는 SQLite를 로컬 저장소로 사용합니다.

>[AZURE.NOTE] 이 섹션을 건너뛰고 모바일 서비스용 Github 샘플 리포지토리의 오프라인 지원이 이미 포함된 예제 프로젝트를 가져올 수 있습니다. 오프라인 지원을 사용하도록 설정한 샘플 프로젝트는 [TodoList 오프라인 샘플](영문)에 있습니다.

1. Windows 8.1 및 Windows Phone 8.1용 SQLite 런타임을 설치합니다. 

    * **Windows 8.1 런타임:** [SQLite for Windows 8.1]을 설치합니다.
    * **Windows Phone 8.1:** [SQLite for Windows Phone 8.1]을 설치합니다.

    >[AZURE.NOTE] Internet Explorer를 사용하는 경우 SQLite를 설치하기 위해 링크를 클릭하면 .vsix를 .zip 파일로 다운로드할지를 묻는 메시지가 표시될 수 있습니다. 파일을 하드 드라이브의 원하는 위치에 .zip 대신 .vsix 확장명으로 저장합니다. Windows 탐색기에서 .vsix 파일을 두 번 클릭하여 설치를 실행합니다.

2. [모바일 서비스 시작](영문) 자습서에서 완료한 프로젝트를 Visual Studio에서 엽니다. Windows 8.1 런타임 및 Windows Phone 8.1 프로젝트용 **WindowsAzure.MobileServices.SQLiteStore** NuGet 패키지를 설치합니다.

    * **Windows 8.1:** 솔루션 탐색기에서 Windows 8.1 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 실행합니다. **SQLiteStore**를 검색하여 `WindowsAzure.MobileServices.SQLiteStore` 패키지를 설치합니다.
    * **Windows Phone 8.1:** Windows Phone 8.1 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 실행합니다. **SQLiteStore**를 검색하여 `WindowsAzure.MobileServices.SQLiteStore` 패키지를 설치합니다.

    >[AZURE.NOTE] 설치에서 이전 버전의 SQLite 대한 참조를 만드는 경우 해당하는 중복 참조를 삭제할 수 있습니다. 

    ![][2]

2. 솔루션 탐색기에서 Windows 8.1 런타임 및 Windows Phone 8.1 플랫폼 프로젝트에 대한 **참조**를 마우스 오른쪽 단추로 클릭하고 SQLite에 대한 참조가 있는지 확인합니다. 이 참조는 **확장** 섹션에 있습니다. 

    ![][1]
    </br>

    **Windows 8.1 런타임**

    ![][11]
    </br>

    **Windows Phone 8.1**

3. SQLite 런타임을 사용하려면 빌드 중인 프로젝트의 프로세서 아키텍처를 **x86**, **x64** 또는 **ARM**으로 변경해야 합니다. **임의 CPU**는 지원되지 않습니다. 솔루션 탐색기의 맨 위에 있는 솔루션을 클릭하고 프로세서 아키텍처 드롭다운 상자를 테스트하려는 지원되는 설정 중 하나로 변경합니다.

    ![][13]

5. 솔루션 탐색기의 공유 프로젝트에서 MainPage.cs 파일을 엽니다. 파일 맨 위에서 다음 using 문의 주석 처리를 제거합니다.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. MainPage.cs에서  `todoTable`의 정의를 주석 처리하고  `MobileServicesClient.GetSyncTable()`을 호출하는 다음 줄에서 하나의 주석 처리를 제거합니다.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync


7. MainPage.cs의 표시된 영역 `Offline sync`에서  `InitLocalStoreAsync` 및  `SyncAsync` 메서드의 주석 처리를 제거합니다. 메서드 `InitLocalStoreAsync`는 SQLite 스토어에서 클라이언트 동기화 컨텍스트를 초기화합니다. 

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8.  `OnNavigatedTo` 이벤트 처리기에서  `InitLocalStoreAsync` 호출의 주석 처리를 제거합니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. 메서드 `InsertTodoItem`, `UpdateCheckedTodoItem`, 및  `ButtonRefresh_Click`에서 3번의 `SyncAsync` 호출의 주석 처리를 제거합니다.

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10.  `SyncAsync` 메서드에서 예외 처리기를 추가합니다.

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    이 예에서는 원격 `todoTable`에 있는 모든 레코드를 검색하지만 쿼리를 전달하여 레코드를 필터링할 수도 있습니다.  `PullAsync`에 대한 첫 번째 매개 변수는 증분 동기화에 사용되는 쿼리 ID이며, `UpdatedAt` 타임스탬프를 사용하여 마지막 동기화 이후에 수정된 레코드만 가져옵니다. 쿼리 ID는 앱의 각 논리적 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 쿼리 ID에 `null`을 전달합니다. 그러면 각 끌어오기 작업에서 모든 레코드를 검색하므로 비효율적일 수 있습니다.

    >[AZURE.NOTE] * 모바일 서비스 데이터베이스에서 레코드가 삭제될 때 장치 로컬 저장소에서 도 레코드를 제거하려면 [일시 삭제]를 사용하도록 설정해야 합니다. 그렇지 않으면 앱이 주기적으로 `IMobileServiceSyncTable.PurgeAsync()`을 호출하여 로컬 저장소를 삭제해야 합니다.

    그러면 `MobileServicePushFailedException`이 밀어넣기 및 끌어오기 작업 모두에 대해 발생할 수 있습니다. 끌어오기의 경우 끌어오기 작업에서 내부적으로 푸시를 수행하여 모든 테이블 및 관계가 동기화된 상태인지 확인하기 때문에 이 예외가 발생할 수 있습니다. 다음 자습서인 [모바일 서비스에서 오프라인 데이터 동기화를 사용하여 충돌 처리]에서 이러한 동기화 관련 예외를 처리하는 방법을 보여 줍니다.

11. Visual Studio에서 **F5** 키를 눌러 앱을 다시 빌드하고 실행합니다. 앱은 삽입, 업데이트 및 새로 고침 작업에 대해 동기화 작업을 수행하므로 오프라인 동기화 변경 이전과 동일하게 동작합니다.

## <a name="update-sync"></a>앱의 동기화 동작 업데이트

이 섹션에서는 **새로 고침** 단추를 누를 때만이 아니라 삽입 및 업데이트 작업 시 동기화되지 않도록 앱을 수정합니다. 그런 다음 앱과 모바일 서비스의 연결을 끊어 오프라인 시나리오를 시뮬레이트합니다. 데이터 항목을 추가하면 모바일 서비스에 동기화되지 않고 로컬 저장소에 보관됩니다.

1. 공유 프로젝트에서 App.xaml.cs를 엽니다. 메서드 `InsertTodoItem` 및  `UpdateCheckedTodoItem`을 편집하여  `SyncAsync`에 대한 호출을 주석 처리합니다.

2. 공유 프로젝트에서 App.xaml.cs를 편집합니다. **MobileServiceClient**의 초기화를 주석 처리하고 잘못된 모바일 서비스 URL을 사용하는 다음 줄을 추가합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3.  `InitLocalStoreAsync()`에서  `SyncAsync()`에 대한 호출을 주석 처리하여 앱이 시작 시 동기화를 수행하지 않도록 합니다.

4. **F5** 키를 눌러 응용 프로그램을 빌드 및 실행합니다. 새 todo 항목을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. 새 todo 항목은 모바일 서비스에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 모든 만들기, 읽기, 업데이트, 삭제(CRUD) 작업을 지원하는 모바일 서비스에 연결된 것처럼 동작합니다.

5. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

## <a name="update-online-app"></a>모바일 서비스를 다시 연결하도록 앱 업데이트

이 섹션에서는 앱을 모바일 서비스에 다시 연결합니다. 여기서는 모바일 서비스를 사용하여 오프라인 상태에서 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 새로 고침 단추를 누르면 데이터가 모바일 서비스와 동기화됩니다.

1. 공유 프로젝트에서 App.xaml.cs를 엽니다.  `MobileServiceClient` 이전 초기화의 주석 처리를 제거하여 올바른 모바일 서비스 URL과 앱 키를 다시 추가합니다.

2. **F5** 키를 눌러 앱을 다시 빌드하고 실행합니다. 앱은 이제 모바일 서비스에 연결되지만 데이터는 오프라인 시나리오와 동일하게 보입니다. 이 앱은 로컬 저장소를 가리키는 `IMobileServiceSyncTable`로 항상 작업하기 때문입니다.

3. Microsoft Azure 관리 포털에 로그인하고 데이터베이스에서 모바일 서비스를 찾습니다. 서비스에서 모바일 서비스에 대해 JavaScript 백 엔드를 사용하는 경우 모바일 서비스의 **데이터** 탭에서 데이터를 찾아볼 수 있습니다. 

    모바일 서비스에 .NET 백 엔드를 사용하는 경우 Visual Studio에서 **서버 탐색기** -> **Azure** -> **SQL 데이터베이스**로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 않습니다.

    ![][6]

4. 앱에서 **새로 고침** 단추를 누릅니다. 그러면 앱이 `PushAsync` 및  `PullAsync`를 호출합니다. 이 푸시 작업은 로컬 저장소 항목을 모바일 서비스로 보낸 다음 모바일 서비스에서 새 데이터를 검색합니다.

    푸시 작업은 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행되며 해당 동기화 컨텍스트에 연결된 모든 테이블에서 변경 내용을 푸시합니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다.

    ![][7]

5. 앱에서 로컬 저장소에서 완료할 몇 개 항목 옆의 확인란을 클릭합니다. 

    ![][8]

6. **새로 고침** 단추를 다시 눌러 `SyncAsync`가 호출되도록 합니다. `SyncAsync`는 푸시와 끌어오기를 모두 호출하지만 이 경우에는  `PushAsync`에 대한 호출이 제거되었을 수 있습니다. **끌어오기 작업에서는 항상 푸시 작업을 먼저 수행**하기 때문입니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.

    ![][10] 
  

##요약

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## 다음 단계

* [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]

* [모바일 서비스에서 일시 삭제 사용][일시 삭제]

<!-- Anchors. -->
[오프라인 기능을 지원하도록 앱 업데이트]: #enable-offline-app
[앱의 동기화 동작 업데이트]: #update-sync
[모바일 서비스를 다시 연결하도록 앱 업데이트]: #update-online-app
[다음 단계]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[TodoList 오프라인 샘플]: http://go.microsoft.com/fwlink/?LinkId=394777
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started/#create-new-service
[시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-store-get-started/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Visual Studio 2012용 Windows Phone 8 자습서]:: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data/
[일시 삭제]: /ko-kr/documentation/articles/mobile-services-using-soft-delete/


[모바일 서비스 SDK Nuget](영문): http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite 저장소 nuget](영문): http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0


<!--HONumber=42-->
