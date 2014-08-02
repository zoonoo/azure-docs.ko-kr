

모바일 서비스에서 오프라인 데이터를 사용하여 충돌 처리
======================================================

[Windows 스토어 C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data "Windows 스토어 C#")

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용할 때 데이터를 동기화하고 충돌을 처리하는 방법을 보여 줍니다. 이 자습서에서는 오프라인 데이터와 온라인 데이터를 모두 지원하는 앱을 다운로드하고, 모바일 서비스를 앱에 통합한 다음 Azure 관리 포털에 로그인하여 앱을 실행하면서 데이터베이스를 보고 업데이트합니다.

이 자습서는 이전 자습서인 [오프라인 데이터 시작](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data)의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [오프라인 데이터 시작](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data)을 완료해야 합니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [Windows 스토어 앱 프로젝트 다운로드](#download-app)
2.  [데이터베이스에 대한 기한 열 추가](#add-column)
    -   [.NET 백 엔드 모바일 서비스에 대한 데이터베이스 업데이트](#dotnet-backend)
    -   [JavaScript 모바일 서비스에 대한 데이터베이스 업데이트](#javascript-backend)

3.  [모바일 서비스에 대해 앱 테스트](#test-app)
4.  [백 엔드에서 데이터를 수동으로 업데이트하여 충돌 생성](#handle-conflict)

이 자습서를 사용하려면 Windows 8.1에서 실행 중인 Visual Studio 2013이 필요합니다.

샘플 프로젝트 다운로드
----------------------

이 자습서는 Visual Studio 2013의 Windows 스토어 앱 프로젝트인 [충돌 처리 코드 샘플](http://go.microsoft.com/fwlink/?LinkId=394787)을 기반으로 합니다. 이 앱의 UI는 각 TodoItem에 대해 새 날짜 열이 있다는 점을 제외하고 [오프라인 데이터 시작](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data) 자습서의 앱과 비슷합니다.

1.  [충돌 처리 코드 샘플](http://go.microsoft.com/fwlink/?LinkId=394787)의 C\# 버전을 다운로드합니다.

2.  [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776)을 아직 설치하지 않은 경우 설치합니다.

3.  Visual Studio 2013에서 다운로드한 프로젝트를 엽니다. **F5** 키를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 **Insert a TodoItem**에 일부 텍스트를 입력하고 **Save**를 클릭합니다. 추가하는 todo 항목의 기한을 수정할 수도 있습니다.

앱이 모바일 서비스에 아직 연결되어 있지 않으므로 **푸시** 및 **끌어오기** 단추를 누르면 예외가 발생합니다.

데이터 모델에 열 추가
---------------------

이 섹션에서는 기한 열이 있는 TodoItem 테이블을 포함하도록 모바일 서비스에 대한 데이터베이스를 업데이트합니다. 이 자습서의 뒤에 나오는 섹션에서 동기화 충돌을 생성할 수 있도록 앱을 사용하여 런타임에 항목에 대한 기한을 변경할 수 있습니다.

샘플의 `TodoItem` 클래스는 MainPage.xaml.cs에 정의되어 있습니다. 이 클래스에는 해당 테이블에 대한 동기화 작업을 대상으로 하는 다음 특성이 있습니다.

        [DataTable("TodoWithDate")]

이 테이블을 포함하도록 데이터베이스를 업데이트합니다.

### .NET 백 엔드 모바일 서비스에 대한 데이터베이스 업데이트

모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 다음 단계에 따라 데이터베이스에 대한 스키마를 업데이트합니다.

1.  Visual Studio에서 .NET 백 엔드 모바일 서비스 프로젝트를 엽니다.
2.  Visual Studio용 솔루션 탐색기의 서비스 프로젝트에서 **Models** 폴더를 확장하고 ToDoItem.cs를 엽니다. 다음과 같이 `DueDate` 속성을 추가합니다.

           public class TodoItem : EntityData
           {
             public string Text { get; set; }
             public bool Complete { get; set; }
             public System.DateTime DueDate { get; set; }
           }

3.  Visual Studio의 솔루션 탐색기에서 **App\_Start** 폴더를 확장한 다음 WebApiConfig.cs 파일을 엽니다.

    WebApiConfig.cs 파일에서 기본 데이터베이스 이니셜라이저 클래스는 `DropCreateDatabaseIfModelChanges` 클래스에서 파생됩니다. 즉, 모델을 변경하면 테이블이 삭제되고 새 모델을 수용하는 테이블이 다시 생성됩니다. 테이블의 데이터가 손실되고 테이블이 다시 시드됩니다. `Seed()` 초기화 함수가 새 DueDate 열을 초기화하도록 데이터베이스 이니셜라이저의 Seed 메서드를 수정합니다. WebApiConfig.cs 파일을 저장합니다.

    > [WACOM.NOTE] 기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때마다 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법](./articles/mobile-services-dotnet-backend-use-code-first-migrations)을 참조하십시오.

         new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
         new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  Visual Studio의 솔루션 탐색기에서 **Controllers** 폴더를 확장하고 ToDoItemController.cs를 엽니다. `TodoItemController` 클래스의 이름을 `TodoWithDateController`로 바꿉니다. 그러면 테이블 작업에 대한 REST 끝점이 변경됩니다.

         public class TodoWithDateController : TableController<TodoItem>

5.  Visual Studio의 솔루션 탐색기에서 .NET 백 엔드 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭하여 변경 내용을 게시합니다.

### JavaScript 백 엔드 모바일 서비스에 대한 데이터베이스 업데이트

JavaScript 백 엔드 모바일 서비스에 대해 **TodoWithDate**라는 새 테이블을 추가합니다. JavaScript 백 엔드 모바일 서비스에 대해 **TodoWithDate** 테이블을 추가하려면 다음 단계를 따르십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

2.  모바일 서비스의 **데이터** 탭으로 이동합니다.

3.  페이지 아래쪽에 있는 **만들기**를 클릭하고 **TodoWithDate**라는 새 테이블을 만듭니다.

모바일 서비스에 대해 앱 테스트
------------------------------

이제 모바일 서비스에 대해 앱을 테스트합니다.

1.  Azure 관리 포털의 명령 모음에서 **키 관리**를 클릭하여 모바일 서비스의 응용 프로그램 키를 찾습니다. **응용 프로그램 키**를 복사합니다.

2.  Visual Studio용 솔루션 탐색기의 클라이언트 샘플 프로젝트에서 App.xaml.cs 파일을 엽니다. 모바일 서비스 URL 및 응용 프로그램 키를 사용하도록 **MobileServiceClient** 초기화를 변경합니다.

          public static MobileServiceClient MobileService = new MobileServiceClient(
             "https://your-mobile-service.azure-mobile.net/",
             "Your AppKey"
         );

3.  Visual Studio에서 **F5** 키를 눌러 빌드하고 실행합니다.

4.  앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **저장**을 클릭합니다. 데이터가 로컬 동기화 테이블에 저장되고, 서버에는 저장되지 않습니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png)

5.  데이터베이스의 현재 상태를 보려면 [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하고 **모바일 서비스**를 클릭한 다음 해당 모바일 서비스를 클릭합니다.

-   모바일 서비스에 대해 JavaScript 백 엔드를 사용 중인 경우 **데이터** 탭을 클릭한 후 **TodoWithDate** 테이블을 클릭합니다. 변경 내용을 앱에서 서버로 푸시하지 않았으므로 **찾아보기**를 클릭하여 테이블이 아직 비어 있는지 확인합니다.

	![][1]

-   모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 **구성** 탭을 클릭한 후 SQL 데이터베이스를 클릭합니다. 화면 아래쪽에 있는 **관리**를 클릭하고 SQL Azure 관리 포털에 로그인하여 데이터베이스를 봅니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png)

1.  앱으로 돌아가서 **푸시**를 클릭합니다.

2.  관리 포털의 **TodoItem** 테이블에서 **새로 고침**을 클릭합니다. 이제 앱에 입력한 데이터가 표시됩니다.

	![][3]

백 엔드에서 데이터를 업데이트하여 충돌 생성
-------------------------------------------

실제로 한 앱에서 업데이트를 데이터베이스의 레코드에 푸시한 후 다른 앱에서 해당 레코드의 이전 버전을 기반으로 하는 동일한 레코드에 변경 내용을 푸시하려고 하면 동기화 충돌이 발생합니다. 앱의 인스턴스가 업데이트된 레코드를 끌어오지 않고 동일한 레코드를 업데이트하려고 하면 충돌이 발생하고 앱에서 `MobileServicePreconditionFailedException` 예외가 표시됩니다.

앱을 다른 컴퓨터에 배포하여 앱의 두 인스턴스를 실행함으로써 충돌을 생성하려면 [데이터베이스 충돌 처리](/en-us/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app) 자습서의 배포 지침을 따를 수 있습니다.

다음 단계에서는 Visual Studio에서 데이터베이스를 업데이트하여 충돌을 발생하는 방법을 보여 줍니다.

1.  Visual Studio에서 서버 탐색기를 실행하고 Azure 계정에 연결합니다. Azure 계정에 대한 **SQL 데이터베이스**를 확장합니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png)

2.  목록에서 SQL 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 클릭합니다.
3.  SQL Server 개체 탐색기에서 데이터베이스를 확장하고 **테이블**을 확장합니다. **TodoWithDate** 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 클릭합니다.

4.  항목 중 하나에 대한 **완료** 필드를 True로 변경합니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png)

5.  할 일 앱으로 돌아가서 데이터베이스에서 직접 수정한 것과 동일한 항목을 편집합니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png)

6.  **푸시** 단추를 클릭합니다. 충돌을 해결하는 방법을 묻는 대화 상자가 표시됩니다. 충돌 해결을 위한 옵션 중 하나를 선택합니다.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png)

동기화 충돌 처리를 위한 코드 검토
---------------------------------

충돌을 감지하도록 오프라인 기능을 설정하려면 로컬 데이터베이스와 데이터베이스 전송 개체 모두에 버전 열을 포함해야 합니다. `TodoItem` 클래스에는 다음 멤버가 있습니다.

        [Version]
        public string Version { get; set; }

`__version` 열은 `OnNavigatedTo()` 메서드에 설정된 로컬 데이터베이스에도 지정되어 있습니다.

코드에서 오프라인 동기화 충돌을 처리하려면 `IMobileServiceSyncHandler`를 구현하는 클래스를 만듭니다. `InitializeAsync`에 대한 호출에서 이 유형의 개체를 전달합니다.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**MainPage.xaml.cs**의 `SyncHandler` 클래스는 `IMobileServiceSyncHandler`를 구현합니다. `ExecuteTableOperationAsync` 메서드는 푸시 작업이 서버에 전송될 때마다 호출됩니다. `MobileServicePreconditionFailedException` 유형의 예외가 발생하면 항목의 로컬 버전과 원격 버전 사이에 충돌이 있는 것입니다.

로컬 항목을 기준으로 충돌을 해결하려면 작업을 다시 시도하면 됩니다. 충돌이 발생한 경우 로컬 항목이 서버 버전과 일치하도록 업데이트되므로, 작업을 다시 실행하면 서버 변경 내용을 로컬 변경 내용으로 덮어씁니다.

    await operation.ExecuteAsync(); 

서버 항목을 기준으로 충돌을 해결하려면 `ExecuteTableOperationAsync`에서 되돌립니다. 개체의 로컬 버전이 삭제되고 서버의 값으로 바뀝니다.

푸시 작업을 중지하고 대기 중인 변경 내용은 유지하려면 `AbortPush()` 메서드를 사용합니다.

    operation.AbortPush();

`AbortPush`가 `ExecuteTableOperationAsync`에서 호출되는 경우 현재 작업을 포함하여 현재 푸시 작업은 중지되지만 대기 중인 변경 내용은 모두 유지됩니다. 다음에 `PushAsync()`를 호출하면 변경 내용이 서버에 전송됩니다.

푸시를 취소하면 `PushAsync`는 `MobileServicePushFailedException`을 발생하고 `PushResult.Status` 예외 속성은 `MobileServicePushStatus.CancelledByOperation` 값을 가집니다.

<!-- Anchors. -->
[Download the Windows Store app project]: #download-app
[Create the mobile service]: #create-service
[Add a due date column for the database]: #add-column
[Updating the database for .NET backend mobile services]: #dotnet-backend  
[Updating the database for JavaScript mobile services]: #javascript-backend
[Test the app against a mobile service]: #test-app
[Manually update the data in the backend to create a conflict]: #handle-conflict
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-design-edit.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
[8]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with offline data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app