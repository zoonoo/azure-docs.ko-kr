<properties 
	pageTitle="모바일 서비스에서 오프라인 데이터를 사용하여 충돌 처리(Windows Phone) | 모바일 개발자 센터" 
	description="Windows Phone 응용 프로그램에서 오프라인 데이터를 동기화할 때 Azure 모바일 서비스를 사용하여 충돌을 처리하는 방법에 대해 알아봅니다." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>


# 모바일 서비스에서 오프라인 데이터 동기화를 사용하여 충돌 처리

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##개요

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용할 때 데이터를 동기화하고 충돌을 처리하는 방법을 보여 줍니다. 이 자습서에서는 오프라인 데이터와 온라인 데이터를 모두 지원하는 앱을 다운로드하고, 모바일 서비스를 앱에 통합한 다음 Azure 관리 포털에 로그인하여 앱을 실행하면서 데이터베이스를 보고 업데이트합니다.

이 자습서는 이전 자습서인 [오프라인 데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [오프라인 데이터 시작]을 완료해야 합니다.


##필수 조건

이 자습서를 완료하려면 Visual Studio 2012 및 [Windows Phone 8 SDK]가 있어야 합니다.


##샘플 프로젝트 다운로드



이 자습서는 Visual Studio 2012용 Windows Phone 8 프로젝트인 [충돌 처리 코드 샘플]을 기반으로 합니다. 이 앱의 UI는 각 TodoItem에 새 날짜 열이 있다는 점을 제외하고는 [오프라인 데이터 시작] 자습서의 앱과 유사합니다.

![][0]


1. [충돌 처리 코드 샘플]의 Windows Phone 버전을 다운로드합니다. 

2. [SQLite for Windows Phone 8]을 아직 설치하지 않은 경우 설치합니다.

3. Visual Studio 2012에서 다운로드한 프로젝트를 엽니다. **Windows Phone** > **확장**에서 **SQLite for Windows Phone**에 대한 참조를 추가합니다.

4. Visual Studio 2012에서 **F5** 키를 눌러 빌드하고 디버거에서 앱을 실행합니다.
 
5. 앱에서 새 todo 항목에 대한 텍스트를 입력한 다음 **저장**을 클릭하여 하나씩 저장합니다. 추가하는 todo 항목의 기한을 수정할 수도 있습니다.


앱이 모바일 서비스에 아직 연결되어 있지 않으므로 **푸시** 및 **끌어오기** 단추를 누르면 예외가 발생합니다.


##데이터 모델에 열 추가

이 섹션에서는 기한 열이 있는 TodoItem 테이블을 포함하도록 모바일 서비스에 대한 데이터베이스를 업데이트합니다. 이 자습서의 뒤에 나오는 섹션에서 동기화 충돌을 생성할 수 있도록 앱을 사용하여 런타임에 항목에 대한 기한을 변경할 수 있습니다.

샘플의 `TodoItem` 클래스는 MainPage.xaml.cs에 정의됩니다. 클래스에 해당 테이블에 대한 동기화 작업을 대상으로 하는 다음과 같은 특성이 있어야 합니다.

        [DataTable("TodoWithDate")]

이 테이블을 포함하도록 데이터베이스를 업데이트합니다.

###<a name="dotnet-backend"></a>.NET 백 엔드 모바일 서비스에 대한 데이터베이스 업데이트 

모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 다음 단계에 따라 데이터베이스에 대한 스키마를 업데이트합니다.

1. Visual Studio에서 .NET 백 엔드 모바일 서비스 프로젝트를 엽니다.
2. Visual Studio 솔루션 탐색기의 서비스 프로젝트에서 **Models** 폴더를 확장하고 ToDoItem.cs를 엽니다. `DueDate` 속성을 다음과 같이 추가합니다.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. Visual Studio의 솔루션 탐색기에서 **App_Start** 폴더를 확장한 다음 WebApiConfig.cs 파일을 엽니다.

    WebApiConfig.cs 파일에서 기본 데이터베이스 이니셜라이저 클래스는 `DropCreateDatabaseIfModelChanges` 클래스에서 파생됩니다. 즉, 모델을 변경하면 테이블이 삭제되고 새 모델을 수용하는 테이블이 다시 생성됩니다. 테이블의 데이터가 손실되고 테이블이 다시 시드됩니다. `Seed()` 초기화 함수가 새 DueDate 열을 초기화하도록 데이터베이스 이니셜라이저의 Seed 메서드를 수정합니다. WebApiConfig.cs 파일을 저장합니다.

    >[AZURE.NOTE]기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때마다 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)을 참조하십시오.


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. Visual Studio의 솔루션 탐색기에서 **Controllers** 폴더를 확장하고 ToDoItemController.cs를 엽니다. `TodoItemController` 클래스의 이름을 `TodoWithDateController`(으)로 바꿉니다. 그러면 테이블 작업에 대한 REST 끝점이 변경됩니다.

        public class TodoWithDateController : TableController<TodoItem>
    

5. Visual Studio의 솔루션 탐색기에서 .NET 백 엔드 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭하여 변경 내용을 게시합니다.


### <a name="javascript-backend"></a>JavaScript 백 엔드 모바일 서비스에 대한 데이터베이스 업데이트

JavaScript 백 엔드 모바일 서비스에 대해 **TodoWithDate**라는 새 테이블을 추가합니다. JavaScript 백 엔드 모바일 서비스에 대해 **TodoWithDate** 테이블을 추가하려면 다음 단계를 따르세요.

  1. [Azure 관리 포털]에 로그인합니다. 

  2. 모바일 서비스의 **데이터** 탭으로 이동합니다.

  3. 페이지 아래쪽에 있는 **만들기**를 클릭하고 **TodoWithDate**라는 새 테이블을 만듭니다.


##모바일 서비스에 대해 앱 테스트

이제 모바일 서비스에 대해 앱을 테스트합니다.

1. Azure 관리 포털의 **대시보드** 탭 명령 모음에서 **키 관리**를 클릭하여 모바일 서비스의 응용 프로그램 키를 찾습니다. **응용 프로그램 키**를 복사합니다.

2. Visual Studio용 솔루션 탐색기의 클라이언트 샘플 프로젝트에서 App.xaml.cs 파일을 엽니다. 모바일 서비스 URL 및 응용 프로그램 키를 사용하도록 **MobileServiceClient** 초기화를 변경합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Visual Studio에서 **F5** 키를 눌러 빌드하고 실행합니다.

4. 앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **저장**을 클릭하여 새 todo 항목을 저장합니다. 데이터가 로컬 동기화 테이블에 저장되고, 서버에는 저장되지 않습니다.

    ![][0]

5. 데이터베이스의 현재 상태를 보려면 [Azure 관리 포털]에 로그인하고 **모바일 서비스**를 클릭한 다음 해당 모바일 서비스를 클릭합니다.

  * 모바일 서비스에 대해 JavaScript 백 엔드를 사용 중인 경우 **데이터** 탭을 클릭한 후 **TodoWithDate** 테이블을 클릭합니다. 변경 내용을 앱에서 서버로 푸시하지 않았으므로 **찾아보기**를 클릭하여 테이블이 아직 비어 있는지 확인합니다.

        ![][1]

  *  모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 **구성** 탭을 클릭한 후 SQL 데이터베이스를 클릭합니다. 화면 아래쪽에 있는 **관리**를 클릭하고 다음과 유사한 SQL 쿼리를 실행하여 SQL Azure 관리 포털에 로그인하여 데이터베이스를 봅니다.
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. 앱으로 돌아가서 **푸시**를 클릭합니다.

8. 관리 포털의 **TodoItem** 테이블에서 **새로 고침**을 클릭합니다. 이제 앱에 입력한 데이터가 표시됩니다.

   	![][3]

9. 두 개의 에뮬레이터에서 앱을 실행하여 충돌을 생성하는 다음 섹션에서 **Emulator WVGA 512MB**를 실행합니다.

##백 엔드에서 데이터를 업데이트하여 충돌 생성

실제로 한 앱에서 업데이트를 데이터베이스의 레코드에 푸시한 후 다른 앱에서 해당 레코드의 이전 버전 필드를 사용하는 동일한 레코드에 변경 내용을 푸시하려고 하면 동기화 충돌이 발생합니다. 앱의 인스턴스가 업데이트된 레코드를 끌어오지 않고 동일한 레코드를 업데이트하려고 하면 충돌이 발생하고 앱에서 `MobileServicePreconditionFailedException` 예외가 표시됩니다.

이 섹션에서 앱의 두 가지 인스턴스를 동시에 실행하여 충돌을 생성합니다.


1. **Emulator WVGA 512MB**가 실행되지 않으면 **Ctrl+F5** 키를 눌러 다시 실행합니다.

2. Visual Studio에서, 출력 장치를 **Emulator WVGA**로 변경하고 **F5** 키를 눌러 새 에뮬레이터에서 앱의 두 번째 인스턴스를 실행합니다.
 
    ![][5]
 
   
3. 앱의 두 번째 인스턴스에서 **끌어오기**를 클릭하여 모바일 서비스 데이터베이스와 로컬 저장소를 동기화합니다. 앱의 두 인스턴스는 모두 동일한 데이터를 포함해야 합니다.
 
    ![][6]

4. 앱의 두 번째 인스턴스에서 확인란을 클릭하여 항목 중 하나를 완료한 다음 **푸시**를 클릭하여 원격 데이터베이스로 변경 내용을 푸시합니다. 다음 스크린샷에서, **Pick up James**는 James가 이미 선택되었음을 나타내며 완료되었습니다. 이제 앱의 첫 번째 인스턴스에는 이전 레코드가 포함됩니다.

    ![][9]

5. 앱의 첫 번째 인스턴스에서 이전 레코드에 대한 날짜를 변경한 다음 **푸시**를 클릭하여 이전 레코드로 원격 데이터베이스를 업데이트합니다. 아래의 스크린샷에서 James가 **2014년 5월 10일**에 선택되도록 예약합니다.

    ![][7]

6. **푸시** 단추를 클릭하여 날짜 변경을 커밋하는 경우 충돌이 검색되었음을 나타내는 대화 상자가 표시됩니다. 충돌을 해결하는 방법을 묻는 메시지가 표시됩니다. 충돌 해결을 위한 옵션 중 하나를 선택합니다.

    아래에 나온 시나리오에서는, James가 이미 선택되었습니다. 따라서 **2014년 5월 10일**에 James를 검색하도록 예약할 필요가 없습니다. 앱의 첫 번째 인스턴스가 서버의 레코드로 업데이트된 레코드를 포함하도록 **서버 버전 사용** 옵션이 선택됩니다.

    ![][8]

##동기화 충돌 처리를 위한 코드 검토

충돌을 감지하도록 오프라인 기능을 설정하려면 로컬 데이터베이스와 데이터베이스 전송 개체 모두에 버전 열을 포함해야 합니다. `TodoItem` 클래스에는 다음 멤버가 있습니다.

        [Version]
        public string Version { get; set; }

`__version` 열은 `OnNavigatedTo()` 메서드에 설정된 로컬 데이터베이스에도 지정되어 있습니다.

코드에서 오프라인 동기화 충돌을 처리하려면 `IMobileServiceSyncHandler`을(를) 구현하는 클래스를 만듭니다. `InitializeAsync`에 대한 호출에서 이 유형의 개체를 전달합니다.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**MainPage.xaml.cs**의 `SyncHandler` 클래스는 `IMobileServiceSyncHandler`을(를) 구현합니다. `ExecuteTableOperationAsync` 메서드는 푸시 작업이 서버에 전송될 때마다 호출됩니다. `MobileServicePreconditionFailedException` 유형의 예외가 발생하면 항목의 로컬 버전과 원격 버전 사이에 충돌이 있는 것입니다.

로컬 항목을 기준으로 충돌을 해결하려면 작업을 다시 시도하면 됩니다. 충돌이 발생한 경우 로컬 항목이 서버 버전과 일치하도록 업데이트되므로, 작업을 다시 실행하면 서버 변경 내용을 로컬 변경 내용으로 덮어씁니다.

    await operation.ExecuteAsync(); 

서버 항목을 기준으로 충돌을 해결하려면 `ExecuteTableOperationAsync`에서 되돌립니다. 개체의 로컬 버전이 삭제되고 서버의 값으로 바뀝니다.

푸시 작업을 중지하고 대기 중인 변경 내용은 유지하려면 `AbortPush()` 메서드를 사용합니다.

    operation.AbortPush();

`AbortPush`이(가) `ExecuteTableOperationAsync`에서 호출되는 경우 현재 작업을 포함하여 현재 푸시 작업은 중지되지만 대기 중인 변경 내용은 모두 유지됩니다. 다음에 `PushAsync()`을(를) 호출하면 변경 내용이 서버에 전송됩니다.

푸시를 취소하면 `PushAsync`에서 `MobileServicePushFailedException`을(를) 내어 예외 속성인 `PushResult.Status`이(가) `MobileServicePushStatus.CancelledByOperation` 값을 포함합니다.




<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[충돌 처리 코드 샘플]: http://go.microsoft.com/fwlink/?LinkId=398257
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[오프라인 데이터 시작]: mobile-services-windows-phone-get-started-offline-data.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite for Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Get started with data]: mobile-services-windows-phone-get-started-data.md
 

<!---HONumber=July15_HO3-->