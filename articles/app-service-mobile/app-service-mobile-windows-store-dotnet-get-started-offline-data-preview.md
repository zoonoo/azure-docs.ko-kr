<properties
	pageTitle="Azure 모바일 앱(Windows 스토어)을 사용하여 오프라인 데이터 사용 | 모바일 개발자 센터"
	description="Azure 모바일 앱을 사용하여 Windows 스토어 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다."
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Azure 모바일 앱에서 오프라인 데이터 동기화 사용

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 범용 Windows 스토어 앱에 오프라인 지원을 추가하는 방법을 보여줍니다. 오프라인 지원을 사용하면 오프라인 시나리오의 앱에서 로컬 데이터베이스를 조작할 수 있습니다. 앱이 백 엔드 데이터베이스와 함께 온라인 상태가 되면 오프라인 기능을 사용하여 로컬 변경 내용을 동기화합니다.



이 자습서에서는 Azure 모바일 앱의 오프라인 기능을 지원하도록 [Windows 앱 만들기] 자습서의 범용 앱 프로젝트를 업데이트합니다. 그런 다음 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고, 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [오프라인 기능을 지원하도록 앱 업데이트]
2. [앱의 동기화 동작 업데이트]
3. [모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트]

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* [Windows 앱 만들기][create a windows app] 완료.
* [Azure 모바일 서비스 SDK 버전 2.0.0 이상][azure mobile app sdk nuget]
* [Azure 모바일 서비스 SQLite 저장소 버전 1.0.2 이상][sqlite store nuget]
* [Windows 8.1용 SQLite](www.sqlite.org/downloads)

>[AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a name="review"></a>서버 프로젝트 구성 검토(옵션)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="enable-offline-app"></a>오프라인 기능을 지원하도록 앱 업데이트

Azure 모바일 앱 오프라인 기능을 사용하면 오프라인 시나리오에서 모바일 서비스를 사용하여 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`을(를) 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다. 이 자습서에서는 SQLite를 로컬 저장소로 사용합니다.

1. Windows 8.1 및 Windows Phone 8.1용 SQLite 런타임을 설치합니다.

    * **Windows 8.1 런타임:**: [Windows 8.1용 SQLite]를 설치합니다.
    * **Windows Phone 8.1:** [Windows Phone 8.1용 SQLite]를 설치합니다.

    >[AZURE.NOTE]Internet Explorer를 사용하는 경우 SQLite를 설치하기 위해 링크를 클릭하면 .vsix를 .zip 파일로 다운로드할지를 묻는 메시지가 표시될 수 있습니다. 파일을 하드 드라이브의 원하는 위치에 .zip 대신 .vsix 확장명으로 저장합니다. Windows 탐색기에서 .vsix 파일을 두 번 클릭하여 설치를 실행합니다.

2. Visual Studio에서 [Windows 앱 만들기] 자습서에서 완료한 프로젝트를 엽니다. Windows 8.1 런타임 및 Windows Phone 8.1 프로젝트용 **WindowsAzure.MobileServices.SQLiteStore** NuGet 패키지를 설치합니다.

    * **Windows 8.1:** 솔루션 탐색기에서 Windows 8.1 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 실행합니다. **SQLiteStore**를 검색하여 `WindowsAzure.MobileServices.SQLiteStore` 패키지를 설치합니다.
    * **Windows Phone 8.1:** Windows Phone 8.1 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 클릭하여 NuGet 패키지 관리자를 실행합니다. **SQLiteStore**를 검색하여 `WindowsAzure.MobileServices.SQLiteStore` 패키지를 설치합니다.

    >[AZURE.NOTE]설치에서 이전 버전의 SQLite 대한 참조를 만드는 경우 해당하는 중복 참조를 삭제할 수 있습니다.

3. 솔루션 탐색기에서 Windows 8.1 런타임 및 Windows Phone 8.1 플랫폼 프로젝트에 대한 **참조**를 마우스 오른쪽 단추로 클릭하고 SQLite에 대한 참조가 있는지 확인합니다. 이 참조는 **확장** 섹션에 있습니다.

    ![][1] </br>

    **Windows 8.1 런타임**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite 런타임을 사용하려면 빌드 중인 프로젝트의 프로세서 아키텍처를 **x86**, **x64** 또는 **ARM**으로 변경해야 합니다. **임의 CPU**는 지원되지 않습니다. 솔루션 탐색기의 맨 위에 있는 솔루션을 클릭하고 프로세서 아키텍처 드롭다운 상자를 테스트하려는 지원되는 설정 중 하나로 변경합니다.

    ![][13]

5. 솔루션 탐색기의 공유 프로젝트에서 MainPage.cs 파일을 엽니다. 파일 맨 위에서 다음 using 문의 주석 처리를 제거합니다.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. MainPage.cs에서 `todoTable`의 정의를 주석 처리하고 `MobileServicesClient.GetSyncTable()`을(를) 호출하는 다음 줄에서 하나의 주석 처리를 제거합니다.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. MainPage.cs의 `Offline sync` 표시된 영역에서 `InitLocalStoreAsync` 및 `SyncAsync` 메서드의 주석 처리를 제거합니다. `InitLocalStoreAsync` 메서드는 SQLite 스토어에서 클라이언트 동기화 컨텍스트를 초기화합니다.

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

8. `OnNavigatedTo` 이벤트 처리기에서 `InitLocalStoreAsync` 호출의 주석 처리를 제거합니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. `InsertTodoItem`, `UpdateCheckedTodoItem` 및 `ButtonRefresh_Click` 메서드에서 3번의 `SyncAsync` 호출의 주석 처리를 제거합니다.

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

10. `SyncAsync` 메서드에서 예외 처리기를 추가합니다.

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

    이 예에서는 원격 `todoTable`에 있는 모든 레코드를 검색하지만 쿼리를 전달하여 레코드를 필터링할 수도 있습니다. `PullAsync`에 대한 첫 번째 매개 변수는 증분 동기화에 사용되는 쿼리 ID이며, `UpdatedAt` 타임스탬프를 사용하여 마지막 동기화 이후에 수정된 레코드만 가져옵니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 `null`을(를) 쿼리 ID로 전달합니다. 그러면 각 끌어오기 작업에서 모든 레코드를 검색하므로 비효율적일 수 있습니다.

    `MobileServicePushFailedException`이(가) 푸시 및 끌어오기 작업 둘 다에 대해 발생할 수 있습니다. 끌어오기의 경우 끌어오기 작업에서 내부적으로 푸시를 수행하여 모든 테이블 및 관계가 동기화된 상태인지 확인하기 때문에 이 예외가 발생할 수 있습니다.

11. Visual Studio에서 **F5** 키를 눌러 다시 빌드하고 실행합니다. 앱은 삽입, 업데이트 및 새로 고침 작업에 대해 동기화 작업을 수행하므로 오프라인 동기화 변경 이전과 동일하게 동작합니다.

## <a name="update-sync"></a>앱의 동기화 동작 업데이트

이 섹션에서는 **새로 고침** 단추를 누를 때만이 아니라 삽입 및 업데이트 작업 시 동기화되지 않도록 앱을 수정합니다. 그런 다음 앱과 모바일 앱 백 엔드의 연결을 끊어 오프라인 시나리오를 시뮬레이트합니다. 데이터 항목을 추가하면 모바일 앱 백 엔드에 동기화되지 않고 로컬 저장소에 보관됩니다.

1. 공유 프로젝트에서 App.xaml.cs를 엽니다. `InsertTodoItem` 및 `UpdateCheckedTodoItem` 메서드를 편집하여 `SyncAsync` 호출을 주석 처리합니다.

2. 공유 프로젝트에서 App.xaml.cs를 편집합니다. **MobileServiceClient**의 초기화를 주석 처리하고 잘못된 모바일 앱 URL을 사용하는 다음 줄을 추가합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. `InitLocalStoreAsync()`에서 `SyncAsync()`에 대한 호출을 주석 처리하여 앱 시작 시 동기화를 수행하지 않도록 합니다.

4. **F5**를 눌러 응용 프로그램을 빌드 및 실행합니다. 새 todo 항목을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. 새 todo 항목은 모바일 앱 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 모든 만들기, 읽기, 업데이트, 삭제(CRUD) 작업을 지원하는 모바일 앱 백 엔드에 연결된 것처럼 동작합니다.

5. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

## <a name="update-online-app"></a>모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트

이 섹션에서는 앱을 모바일 앱 백 엔드에 다시 연결합니다. 여기서는 모바일 앱 백 엔드를 사용하여 오프라인 상태에서 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 새로 고침 단추를 누르면 데이터가 모바일 앱 백 엔드와 동기화됩니다.

1. 공유 프로젝트에서 App.xaml.cs를 엽니다. `MobileServiceClient`의 이전 초기화 주석 처리를 제거하여 올바른 모바일 앱 서비스 백 엔드 URL, 게이트웨이 URL 및 앱 키를 다시 추가합니다.

2. **F5**를 눌러 앱을 다시 빌드하고 실행합니다. 앱은 이제 모바일 앱 백 엔드에 연결되지만 데이터는 오프라인 시나리오와 동일하게 보입니다. 이 앱은 항상 로컬 저장소를 가리키는 `IMobileServiceSyncTable`(으)로 작업하기 때문입니다.

3. Azure 포털에 로그인하고 모바일 앱 백 엔드에 대한 데이터베이스를 조회합니다.

    Visual Studio에서 **서버 탐색기** -> **Azure** -> **SQL 데이터베이스**로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 않습니다.

4. 앱에서 **새로 고침** 단추를 누릅니다. 그러면 앱이 `PushAsync` 및 `PullAsync`을(를) 호출합니다. 이 푸시 작업은 로컬 저장소 항목을 모바일 서비스로 보낸 다음 모바일 서비스에서 새 데이터를 검색합니다.

    푸시 작업은 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행되며 해당 동기화 컨텍스트에 연결된 모든 테이블에서 변경 내용을 푸시합니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다.

5. 앱에서 로컬 저장소에서 완료할 몇 개 항목 옆의 확인란을 클릭합니다.

6. **새로 고침** 단추를 다시 눌러 `SyncAsync`이(가) 호출되도록 합니다. `SyncAsync`은(는) 푸시와 끌어오기를 모두 호출하지만 이 경우에는 `PushAsync`에 대한 호출이 제거되었을 수 있습니다. **끌어오기 작업에서는 항상 푸시 작업을 먼저 수행하기** 때문입니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.


##요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`을(를) 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync` 및 `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

*  서버의 변경 내용을 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`을(를) 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

* 서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`을(를) 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.

    클라이언트에 저장된 데이터를 필터링하기 위해 쿼리를 지정할 수 있는 `PullAsync()`의 오버로드도 있습니다. 쿼리가 전달되지 않으면 `PullAsync()`은(는) 해당 테이블(또는 쿼리)에 모든 행을 끌어옵니다. 쿼리를 전달하여 앱에서 동기화해야 하는 변경 사항만 필터링할 수 있습니다.

* 증분 동기화를 사용하려면 쿼리 ID를 `PullAsync()`(으)로 전달합니다. 쿼리 ID는 마지막 풀링 작업의 결과에서 마지막으로 업데이트된 타임스탬프를 저장하는 데 사용됩니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 쿼리에 매개 변수가 있으면 동일한 매개 변수 값이 쿼리 ID의 일부여야 합니다.

    예를 들어 userid를 필터링할 경우 이는 쿼리 ID의 일부여야 합니다.

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    증분 동기화를 옵트아웃하려면 `null`을(를) 쿼리 ID로 전달합니다. 이 경우 `PullAsync`에 대한 모든 호출에서 모든 레코드가 검색되므로 이는 잠재적으로 비효율적입니다.

* 앱이 주기적으로 `IMobileServiceSyncTable.PurgeAsync()`을(를) 호출하여 로컬 저장소를 삭제합니다.


<!-- Anchors. -->
[오프라인 기능을 지원하도록 앱 업데이트]: #enable-offline-app
[앱의 동기화 동작 업데이트]: #update-sync
[모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Windows 앱 만들기]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Windows 8.1용 SQLite]: http://go.microsoft.com/fwlink/?LinkId=394776
[Windows Phone 8.1용 SQLite]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=August15_HO6-->