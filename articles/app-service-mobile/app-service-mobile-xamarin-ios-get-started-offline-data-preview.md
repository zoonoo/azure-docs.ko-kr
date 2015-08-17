<properties
    pageTitle="모바일 앱(Xamarin iOS)에 대해 오프라인 동기화 사용"
    description="앱 서비스 모바일 앱을 사용하여 Xamarin iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="07/01/2015"
    ms.author="donnam"/>

# Xamarin iOS 모바일 앱에 대해 오프라인 동기화 사용

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

이 자습서에서는 iOS용 모바일 앱의 오프라인 동기화 기능을 설명합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 서비스와 동기화됩니다.

오프라인 동기화의 몇 가지 잠재적인 용도는 다음과 같습니다.

* 서버 데이터를 장치에 로컬로 캐시하여 앱 응답성 향상
* 일시적인 네트워크 연결 중단에도 앱 복원력 제공
* 최종 사용자가 네트워크에 액세스할 수 없는 경우에도 데이터를 만들고 수정할 수 있도록 허용하여 네트워크에 연결되지 않은 시나리오까지 지원
* 여러 장치 간에 데이터를 동기화하고 동일한 레코드를 두 개의 장치에서 수정할 때 충돌 감지

모바일 앱을 처음 사용하는 경우 먼저 [Xamarin iOS 앱 만들기] 자습서를 완료합니다.

이 자습서를 사용하려면 다음이 필요합니다.

* Visual Studio 2013
* Visual Studio [Xamarin 확장] **또는** OS X의 [Xamarin Studio]

##<a name="review"></a>서버 프로젝트 구성 검토(옵션)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## 모바일 앱 동기화 코드 검토

모바일 앱 오프라인 동기화를 사용하면 네트워크에 액세스할 수 없는 경우에도 최종 사용자가 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다. 이 섹션에서는 `QSTodoService.cs`의 오프라인 동기화 관련 코드를 안내합니다.

1. [모바일 앱 시작] 자습서에서 완성한 프로젝트를 Visual Studio에서 엽니다. `QSTodoService.cs` 파일을 엽니다.

2. `todoTable` 멤버의 유형이 `IMobileServiceSyncTable`인지 확인합니다. 오프라인 동기화에서는 `IMobileServiceTable` 대신 이 동기화 테이블 인터페이스를 사용합니다. 동기화 테이블을 사용하면 모든 작업이 로컬 저장소로 이동하고 명시적 푸시 및 끌어오기 작업이 있는 원격 서비스와만 동기화됩니다.

    동기화 테이블에 대한 참조를 가져오려면 `GetSyncTable()` 메서드를 사용합니다. 오프라인 동기화 기능을 제거하려면 대신 `GetTable()`을(를) 사용합니다.

3. 모든 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다. 이 작업은 `InitializeStoreAsync` 메서드에서 수행됩니다.

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    이 코드는 모바일 앱 SDK에 제공된 `MobileServiceSQLiteStore` 클래스를 사용하여 로컬 저장소를 만듭니다. `IMobileServiceLocalStore`를 구현하여 다양한 로컬 저장소 구현을 제공할 수도 있습니다.

    `DefineTable` 메서드는 제공된 형식(이 경우 `ToDoItem`)의 필드와 일치하는 테이블을 로컬 저장소에 만듭니다. 이 형식은 원격 데이터베이스에 있는 열을 모두 포함하지 않아도 되며, 열의 하위 집합만 저장할 수 있습니다.

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. `SyncAsync` 메서드는 실제 동기화 작업을 트리거합니다.

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    먼저 `IMobileServiceSyncContext.PushAsync()`에 대해 호출합니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다. CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

    다음으로 이 메서드는 `IMobileServiceSyncTable.PullAsync()`에 대해 호출하여 서버의 테이블에서 앱으로 데이터를 끌어옵니다. 동기화 컨텍스트에 보류 중인 변경 내용이 있으면 끌어오기에서 항상 푸시 작업을 먼저 수행합니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다. 이 경우 명시적으로 푸시를 호출했습니다.

    이 예제에서는 원격 `TodoItem` 테이블에 있는 모든 레코드를 검색하지만 쿼리를 전달하여 레코드를 필터링할 수도 있습니다. `PullAsync()`의 첫 번째 매개 변수는 증분 동기화에 사용되는 쿼리 ID이며, `UpdatedAt` 타임스탬프를 사용하여 마지막 동기화 이후에 수정된 레코드만 가져옵니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 `null`을(를) 쿼리 ID로 전달합니다. 그러면 각 끌어오기 작업에서 모든 레코드를 검색하므로 비효율적일 수 있습니다.

<!--     >[AZURE.NOTE] 모바일 서비스 데이터베이스에서 삭제한 레코드를 해당 장치의 로컬 저장소에서 삭제하려면 [소프트 삭제]를 활성화해야 합니다. 그렇지 않은 경우 해당 응용프로그램은 정기적으로 `IMobileServiceSyncTable.PurgeAsync()` 를 호출해야만 해당 로컬 저장소를 삭제할 수 있습니다.

    푸시와 풀 작업 모두에서 'MobileServicePushFailedException` 이 발생할 수 있습니다. 다음 [모바일 서비스를 위한 오프라인 지원과 관련된 충돌 처리 방법] 자습서에는 예외와 관련된 이러한 동기화를 처리하는 방법이 나와 있습니다.
-->

5. `QSTodoService` 클래스에서 `SyncAsync()` 메서드는 데이터, `InsertTodoItemAsync()` 및 `CompleteItemAsync`을 수정하는 작업 뒤에 호출됩니다. 사용자가 새로 고침 제스처를 수행할 때마다 최신 데이터를 가져오도록 `RefreshDataAsync()`에서도 호출됩니다. `QSTodoListViewController.ViewDidLoad()`이(가) `RefreshDataAsync()`을(를) 호출하므로 앱이 시작될 때도 동기화를 수행합니다.

    `SyncAsync()`은(는) 데이터가 수정될 때마다 호출되므로 이 앱은 데이터를 편집할 때마다 사용자가 온라인 상태인 것으로 가정합니다. 다음 섹션에서는 사용자가 오프라인 상태에서도 편집할 수 있도록 앱을 업데이트합니다.

## 앱의 동기화 동작 업데이트

이 섹션에서는 앱 시작 또는 삽입 및 업데이트 작업 시 앱이 동기화되지 않고 새로 고침 제스처를 할 때만 동기화되도록 앱을 수정합니다. 그런 다음 앱과 모바일 서비스의 연결을 끊어 오프라인 시나리오를 시뮬레이트합니다. 데이터 항목을 추가하면 모바일 백 엔드 데이터 저장소에 바로 동기화되지 않고 로컬 저장소에 보관됩니다.

1. `QSTodoService.cs`를 엽니다. 다음 메서드에서 `SyncAsync()` 호출을 주석으로 처리합니다.

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    이제 `RefreshAsync()`가 로컬 저장소에서 데이터를 로드하기만 하고 앱 백 엔드에 연결하지 않습니다.

2. `QSTodoService.cs`에서 잘못된 모바일 앱 URI를 가리키도록 `applicationURL`의 정의를 변경합니다.

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. 새로 고침 제스처가 수행될 때 데이터를 동기화하려면 `QSTodoListViewController.RefreshAsync()` 메서드를 편집합니다. `SyncAsync()` 호출을 `RefreshDataAsync()` 호출 앞에 추가합니다.

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. 앱을 빌드 및 실행합니다. 일부 새 할 일 항목을 추가합니다. 이러한 새 항목은 모바일 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 백 엔드에 연결된 것처럼 동작하며 모든 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 지원합니다.

5. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

## 모바일 백 엔드를 다시 연결하도록 앱 업데이트

이 섹션에서는 앱을 모바일 백 엔드에 다시 연결하여 다시 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 새로 고침 제스처를 수행하면 데이터가 모바일 백 엔드에 동기화됩니다.

1. `QSTodoService.cs`를 엽니다. 잘못된 모바일 앱 URL을 제거하고 올바른 URL과 앱 키를 다시 추가합니다.

2. 앱을 다시 빌드하고 실행합니다. 이제 앱이 모바일 백 엔드에 연결되었지만 데이터가 변경되지 않았습니다. 이 앱은 로컬 저장소를 가리키는 `IMobileServiceSyncTable`를 항상 사용하기 때문입니다.

3. 백 엔드 SQL 데이터베이스에 연결하여 저장된 데이터를 확인합니다. Visual Studio에서 **서버 탐색기** -> **Azure** -> **SQL 데이터베이스**로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 *않습니다*.

4. 앱에서 항목 목록을 아래로 끌어서 새로 고침 제스처를 수행합니다. 그러면 앱에서 `RefreshDataAsync()`를 호출하고, 여기서 다시`SyncAsync()`를 호출합니다. 그러면 푸시 및 끌어오기 작업을 수행하여 먼저 로컬 저장소 항목을 모바일 백 엔드로 보낸 다음 백 엔드에서 새 데이터를 검색합니다.

5. 데이터베이스 뷰를 새로 고치고 변경 내용이 동기화되었는지 확인합니다.

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS 앱 만들기]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin 확장]: http://xamarin.com/visual-studio
 

<!---HONumber=August15_HO6-->