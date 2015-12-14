<properties
	pageTitle="Azure 모바일 앱(Windows 8.1)에 대해 오프라인 동기화 사용 | Microsoft Azure"
	description="Azure 모바일 앱을 사용하여 Windows 스토어 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/22/2015"
	ms.author="wesmc"/>

# Windows 앱에 대해 오프라인 동기화 사용

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## 개요

이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 Windows 8.1 스토어 또는 Phone 앱에 오프라인 지원을 추가하는 방법을 보여줍니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 백 엔드와 동기화됩니다.

이 자습서에서는 Azure 모바일 앱의 오프라인 기능을 지원하도록 자습서 [Windows 앱 만들기]의 Windows 8.1 앱 프로젝트를 업데이트합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화] 항목을 참조하세요.

## 요구 사항

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* [Windows 앱 만들기][create a windows app] 완료.
* [Azure 모바일 서비스 SQLite 저장소][sqlite store nuget]
* [Windows 8.1용 SQLite](http://www.sqlite.org/downloads)

## 오프라인 기능을 지원하도록 클라이언트 앱 업데이트

Azure 모바일 앱 오프라인 기능을 사용하면 오프라인 시나리오에서 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`을(를) 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다. 이 자습서에서는 SQLite를 로컬 저장소로 사용합니다.

1. Windows 8.1 및 Windows Phone 8.1용 SQLite 런타임을 설치합니다.

    * **Windows 8.1 런타임:**: [Windows 8.1용 SQLite]를 설치합니다.
    * **Windows Phone 8.1:** [Windows Phone 8.1용 SQLite]를 설치합니다.

    >[AZURE.NOTE]이러한 지침은 Windows 10 UAP 프로젝트에 대해서도 적용되지만 [Windows 10용 SQLite]를 대신 설치해야 합니다.

2. Visual Studio에서 [Windows 앱 만들기] 자습서에서 완료한 프로젝트를 엽니다. Windows 8.1 런타임 및 Windows Phone 8.1 프로젝트용 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 패키지를 설치합니다. Windows 스토어 8.1 및 Windows Phone 8.1 프로젝트 모두에 NuGet 참조를 추가합니다.

    >[AZURE.NOTE]설치가 추가 참조를 설치한 SQLite의 다른 버전에 만드는 경우 컴파일 오류가 발생합니다. 프로젝트의 **참조** 노드에서 중복을 제거하여 이 오류를 해결해야 합니다.

3. 솔루션 탐색기에서 Windows 8.1 런타임 및 Windows Phone 8.1 플랫폼 프로젝트에 대한 **참조**를 마우스 오른쪽 단추로 클릭하고 SQLite에 대한 참조가 있는지 확인합니다. 이 참조는 **확장** 섹션에 있습니다.

    ![][1] </br>

    **Windows 8.1 런타임**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite는 네이티브 라이브러리이며 **x86**, **x64** 또는 **ARM**과 같은 플랫폼 특정 아키텍처를 선택해야 합니다. **임의 CPU**는 지원되지 않습니다. 솔루션 탐색기의 맨 위에 있는 솔루션을 클릭하고 프로세서 아키텍처 드롭다운 상자를 테스트하려는 지원되는 설정 중 하나로 변경합니다.

    ![][13]

5. 솔루션 탐색기의 공유 프로젝트에서 MainPage.cs 파일을 엽니다. 파일 맨 위에서 다음 using 문의 주석 처리를 제거합니다.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. MainPage.cs에서 `todoTable`을 `IMobileServiceTable`로 초기화하는 코드 줄을 주석으로 처리합니다. `todoTable`을 `IMobileServiceSyncTable`로 초기화하는 코드 줄의 주석 처리를 제거합니다.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. MainPage.cs의 `Offline sync` 표시된 영역에서 `InitLocalStoreAsync` 및 `SyncAsync` 메서드의 주석 처리를 제거합니다. `InitLocalStoreAsync` 메서드는 SQLite 스토어에서 클라이언트 동기화 컨텍스트를 초기화합니다. Visual Studio에서 주석으로 처리된 모든 줄을 선택하고 **Ctrl**+**K**+**U** 바로 가기 키를 사용하여 주석 처리를 제거합니다.

	`SyncAsync`에서 푸시 작업이 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행됩니다. 모든 테이블에 대한 클라이언트에서 수행한 변경 내용을 컨텍스트가 추적하기 때문입니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다. 이 동작에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화]를 참조하세요.

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

10. `SyncAsync` 메서드에서 예외 처리기를 추가합니다. 오프라인 상황에서 `MobileServicePushFailedException`은 `PushResult.Status == CancelledByNetworkError`를 사용하여 발생합니다.

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
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
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

    이 `PullAsync` 예제에서는 원격 `todoTable`에 있는 모든 레코드를 검색하지만 쿼리를 전달하여 레코드를 필터링할 수도 있습니다. `PullAsync`에 대한 첫 번째 매개 변수는 증분 동기화에 사용되는 쿼리 ID이며, `UpdatedAt` 타임스탬프를 사용하여 마지막 동기화 이후에 수정된 레코드만 가져옵니다. 쿼리 ID는 클라이언트 응용 프로그램의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 `null`을 쿼리 ID로 전달합니다. 그러면 각 끌어오기 작업에서 모든 레코드를 검색하므로 비효율적일 수 있습니다.

    `MobileServicePushFailedException`이(가) 푸시 및 끌어오기 작업 둘 다에 대해 발생할 수 있습니다. 끌어오기의 경우 끌어오기 작업에서 내부적으로 푸시를 수행하여 모든 테이블 및 관계가 동기화된 상태인지 확인하기 때문에 이 예외가 발생할 수 있습니다.

11. Visual Studio에서 **F5** 키를 눌러 클라이언트 앱을 다시 빌드하고 실행합니다. 앱은 삽입, 업데이트 및 새로 고침 작업에 대해 동기화 작업을 수행하므로 오프라인 동기화 변경 이전과 동일하게 동작합니다. 그러나 오프라인 시나리오에서 사용할 수 있는 로컬 데이터베이스를 채웁니다. 다음 섹션에서 오프라인 시나리오가 발생하면 이제 로컬 데이터베이스가 채워집니다.

## <a name="update-sync"></a>클라이언트 앱의 동기화 동작 업데이트

Azure 모바일 앱 백 엔드에 연결을 끊어서 오프라인 시나리오를 시뮬레이션하도록 클라이언트 앱을 수정합니다. 데이터 항목을 추가하면 예외 처리기는 앱이 `PushResult.Status == CancelledByNetworkError`를 사용하여 오프라인 모드로 작동 중임을 사용자에게 알립니다. 추가된 항목은 로컬 저장소에 보관되지만 다시 온라인 상태가 되고 Azure 모바일 앱 백 엔드에 푸시를 성공적으로 실행할 때까지 모바일 앱 백 엔드에 동기화되지 않습니다.

1. 공유 프로젝트에서 App.xaml.cs를 편집합니다. **MobileServiceClient**의 초기화를 주석 처리하고 잘못된 모바일 앱 URL을 사용하는 다음 줄을 추가합니다.

         public static MobileServiceClient MobileService = 
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	응용 프로그램이 인증 또한 사용하는 경우 로그인에 실패할 수 있습니다. 장치에서 wifi 및 celluar 네트워크를 사용하지 않도록 설정하여 오프라인 동작을 시연하거나 비행기 모드를 사용할 수 있습니다.

2. **F5**를 눌러 응용 프로그램을 빌드 및 실행합니다. 앱을 시작하는 경우 동기화는 새로 고침에 실패합니다.
3. 새 todo 항목을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. `PushResult.Status=CancelledByNetworkError`를 사용하는 각각에 대해 푸시가 실패합니다. 새 todo 항목은 모바일 앱 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 
 
	`PushResult.Status=CancelledByNetworkError`에 대한 예외 대화 상자를 무시할 수 있습니다. 그러면 클라이언트 앱은 모든 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 원활하게 지원하는 모바일 앱 백 엔드에 연결된 것처럼 동작합니다.

4. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

5. (옵션) Visual Studio에서 **서버 탐색기**를 엽니다. **Azure**->**SQL 데이터베이스**에 있는 데이터베이스로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다. 이제 SQL 데이터베이스 테이블 및 콘텐츠를 찾아볼 수 있습니다. 백 엔드 데이터베이스의 데이터가 변경되지 않은 것을 확인합니다.

6. (옵션) Fiddler 또는 Postman과 같은 REST 도구를 사용하여 `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` 형식의 GET 쿼리를 통해 모바일 백 엔드를 쿼리합니다.

## <a name="update-online-app"></a>모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트

이 섹션에서는 앱을 모바일 앱 백 엔드에 다시 연결합니다. 여기서는 모바일 앱 백 엔드를 사용하여 오프라인 상태에서 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 응용 프로그램을 처음 실행하는 경우 `OnNavigatedTo` 이벤트 처리기는 `InitLocalStoreAsync`를 호출합니다. 차례로 `SyncAsync`를 호출하여 백 엔드 데이터베이스와 로컬 저장소를 동기화합니다. 따라서 앱이 시작할 때 동기화하려고 합니다.

1. 공유 프로젝트에서 App.xaml.cs를 엽니다. `MobileServiceClient`의 이전 초기화에서 주석 처리를 제거하여 올바른 모바일 앱 URL과 게이트웨이 URL을 사용합니다.

2. **F5**를 눌러 앱을 다시 빌드하고 실행합니다. 앱은 푸시 및 끌어오기 작업을 사용하여 `OnNavigatedTo` 이벤트 처리기가 실행되는 즉시 로컬 변경 내용을 Azure 모바일 앱 백 엔드와 동기화합니다.

3. (선택 사항) SQL Server 개체 탐색기 또는 Fiddler와 같은 REST 도구를 사용하여 업데이트된 데이터를 봅니다. Azure 모바일 앱 백 엔드 데이터베이스와 로컬 저장소 간에 데이터는 동기화되었습니다.

4. 앱에서 로컬 저장소에서 완료할 몇 개 항목 옆의 확인란을 클릭합니다.

  `UpdateCheckedTodoItem`은 `SyncAsync`를 호출하여 모바일 앱 백 엔드와 전체 항목을 동기화합니다. `SyncAsync`는 푸시와 끌어오기를 둘 다 호출합니다. 그러나 **클라이언트가 변경한 테이블에 끌어오기를 실행할 때마다 클라이언트 동기화 컨텍스트에 푸시가 항상 먼저 자동으로 실행됩니다**. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다. 따라서 이 경우에 끌어오기를 실행할 때 자동으로 실행되기 때문에 `PushAsync` 호출을 제거할 수 있습니다. 인식하지 못하면 이 동작으로 예기치 않은 푸시가 발생할 수 있습니다. 이 동작에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화]를 참조하세요.


##요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`을(를) 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync` 및 `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

*  서버의 변경 내용을 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`을(를) 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

* 서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`을(를) 호출했습니다.

    클라이언트 동기화 컨텍스트가 해당 테이블에 변경된 내용을 추적하는 경우 끌어오기는 항상 먼저 동기화 컨텍스트 푸시를 발생시킵니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.

    클라이언트에 저장된 데이터를 필터링하기 위해 쿼리를 지정할 수 있는 `PullAsync()`의 오버로드도 있습니다. 쿼리가 전달되지 않으면 `PullAsync()`은(는) 해당 테이블(또는 쿼리)에 모든 행을 끌어옵니다. 쿼리를 전달하여 앱에서 동기화해야 하는 변경 사항만 필터링할 수 있습니다.

* 증분 동기화를 사용하려면 쿼리 ID를 `PullAsync()`(으)로 전달합니다. 쿼리 ID는 마지막 풀링 작업의 결과에서 마지막으로 업데이트된 타임스탬프를 저장하는 데 사용됩니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 쿼리에 매개 변수가 있으면 동일한 매개 변수 값이 쿼리 ID의 일부일 수 있습니다.

    예를 들어 userid를 필터링할 경우 사용자 고유 쿼리 ID는 다음과 같을 수 있습니다.

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    증분 동기화를 옵트아웃하려면 `null`을(를) 쿼리 ID로 전달합니다. 이 경우 `PullAsync`에 대한 모든 호출에서 모든 레코드가 검색되므로 이는 잠재적으로 비효율적입니다.

* 앱이 주기적으로 `IMobileServiceSyncTable.PurgeAsync()`을(를) 호출하여 로컬 저장소를 삭제합니다.


## 추가 리소스

* [Azure 모바일 앱에서 오프라인 데이터 동기화]

* [Cloud Cover: Azure 모바일 서비스에서 오프라인 동기화](참고: 비디오는 모바일 서비스에 있지만 Azure 모바일 앱에서 비슷한 방식으로 오프라인 동기화가 작동합니다.)

* [Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure 모바일 앱에서 오프라인 데이터 동기화]: ../app-service-mobile-offline-data-sync.md
[create a windows app]: ../app-service-mobile-windows-store-dotnet-get-started.md
[Windows 앱 만들기]: ../app-service-mobile-windows-store-dotnet-get-started.md
[Windows 8.1용 SQLite]: http://go.microsoft.com/fwlink/?LinkID=716919
[Windows Phone 8.1용 SQLite]: http://go.microsoft.com/fwlink/?LinkID=716920
[Windows 10용 SQLite]: http://go.microsoft.com/fwlink/?LinkID=716921

[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
 
[Cloud Cover: Azure 모바일 서비스에서 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_1203_2015--->