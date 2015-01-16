<properties urlDisplayName="Using Offline Data" pageTitle="모바일 서비스에서 오프라인 데이터 사용(Xamarin iOS) | 모바일 개발자 센터" metaKeywords="" description="Azure 모바일 서비스를 사용하여 Xamarin iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# 모바일 서비스에서 오프라인 데이터 사용

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용하는 방법을 보여 줍니다. 이러한 기능을 사용하면 모바일 서비스의 오프라인 시나리오의 경우 로컬 데이터베이스를 조작할 수 있습니다. 온라인으로 다시 전환되면 오프라인 기능을 사용하여 로컬 변경을 모바일 서비스와 동기화할 수 있습니다. 

이 자습서에서는 [모바일 서비스 시작] 또는 [데이터 작업 시작] 자습서에서 작성한 앱을 업데이트하여 Azure 모바일 서비스의 오프라인 기능을 지원합니다. 그런 다음, 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

>[WACOM.NOTE] 이 자습서는 Windows 스토어 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 빠른 시작에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.
>
>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요. 

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [오프라인 기능을 지원하도록 앱 업데이트]
2. [모바일 서비스에 연결된 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

* XCode 4.5 및 iOS 6.0(또는 이후 버전) 
* [Xamarin 확장]**이 포함된 Visual Studio 또는 OS X의** [Xamarin Studio]
* [모바일 서비스 시작] 또는 [데이터 작업 시작] 자습서 완료
* [Azure 모바일 서비스 SDK 버전 1.3.0-beta2(이상)][Mobile Services SDK Nuget]
* [Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-beta2(이상)][SQLite store nuget]

>[WACOM.NOTE] 아래의 지침에서는 Xamarin 확장이 포함된 Visual Studio 2012 이상을 사용 중이라고 가정합니다. OS X의 Xamarin Studio를 사용 중인 경우 기본 제공 NuGet 패키지 관리자 지원을 사용하세요.

## <a name="enable-offline-app"></a>오프라인 기능을 지원하도록 앱 업데이트

네트워크에 액세스할 수 없는 경우 최종 사용자는 Azure 모바일 서비스 오프라인 동기화를 통해 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다.

이 자습서가 완료된 상태인 프로젝트는 [여기](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS)(영문)서 사용할 수 있습니다.

1. Visual Studio에서 [모바일 서비스 시작] 또는 [데이터 작업 시작] 자습서에서 완료한 프로젝트를 엽니다. 솔루션 탐색기의 **구성 요소onents**에서 **Azure 모바일 서비스 SDK**에 대한 참조를 제거합니다.

2. 패키지 관리자 콘솔에서 다음 명령을 사용하여 모바일 서비스 SQLiteStore의 시험판 패키지를 설치합니다. 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    그러면 모든 필요한 종속 항목도 설치됩니다.
    
3. 참조 노드에서 `System.IO`, `System.Runtime` 및 `System.Threading.Tasks`에 대한 참조를 제거합니다.

### QSTodoService.cs 파일 편집 

SQLite 로컬 저장소에서 모바일 서비스 오프라인 기능을 사용할 수 있도록 `QSTodoService` 클래스를 편집합니다.

1. 파일 맨 위에 다음 using 문을 추가합니다.

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. `todoTable` 멤버의 형식을 `IMobileServiceTable`에서 `IMobileServicesSyncTable`로 변경합니다.

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. `QSTodoService`의 생성자에서 `todoTable`의 이니셜라이저를 변경합니다.

        todoTable = client.GetSyncTable <ToDoItem> ();

4. `QSTodoService`의 생성자에서 코드의 두 번째 줄로 `SQLitePCL.CurrentPlatform.Init()`에 대한 호출을 추가합니다.

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. `QSTodoService` 클래스에서 새 메서드 `InitializeAsync`를 정의합니다.
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. `QSTodoService` 클래스에서 새 메서드 `SyncAsync`를 정의합니다.
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### ToDoItem.cs 편집 

1. using 문을 추가합니다. 

        using Microsoft.WindowsAzure.MobileServices; 


2. 다음 멤버를 `ToDoItem` 클래스에 추가합니다.
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### QSTodoListViewController.cs 편집 

사용자가 새로 고침 제스처를 수행할 때 새 `SyncAsync` 메서드를 호출하도록 `QSTodoListViewController`를 수정합니다.
 
1. `ViewDidLoad()`에서 `todoService` 초기화 뒤에 `InitializeStoreAsync`에 대한 호출을 추가합니다.

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. `RefreshAsync`에 대한 호출 전에 `SyncAsync`를 호출하도록 `AddRefreshControl` 메서드를 수정합니다.


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>앱 테스트 

이 섹션에서는 로컬 저장소를 모바일 서비스 데이터베이스와 동기화하는 `SyncAsync` 메서드를 테스트합니다.

1. Visual Studio에서 **실행** 단추를 눌러 프로젝트를 빌드하고 이 프로젝트의 기본값인 iPhone 에뮬레이터에서 앱을 시작합니다.

2. 앱의 항목 목록은 비어 있습니다. 이전 섹션의 코드 변경으로 인해 앱이 더 이상 모바일 서비스에서 항목을 읽지 않지만, 로컬 저장소에서는 항목을 읽습니다. 

3. ToDo 목록에 항목 추가

    ![][1]


4. Microsoft Azure 관리 포털에 로그인하고 데이터베이스에서 모바일 서비스를 찾습니다. 서비스에서 모바일 서비스에 대해 JavaScript 백 엔드를 사용하는 경우 모바일 서비스의 **데이터** 탭에서 데이터를 찾아볼 수 있습니다. 모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 SQL Azure 확장 프로그램에서 데이터베이스의 **관리** 단추를 클릭하여 테이블에 대한 쿼리를 실행할 수 있습니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 않습니다.

5. 앱에서 항목 목록을 아래로 끌어서 새로 고침 제스처를 수행합니다. 그러면 앱이 `MobileServiceClient.SyncContext.PushAsync`, `IMobileServiceSyncTable.PullAsync()` 및 `RefreshTodoItems`를 차례로 호출하여 로컬 저장소에 있는 항목으로 앱을 새로 고칩니다. 

    푸시 작업은 모바일 서비스 데이터베이스에서 저장소의 데이터를 받습니다. 이 작업은 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행되며 해당 동기화 컨텍스트에 연결된 모든 테이블에서 변경 내용을 푸시합니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다.
    
    반대로, 끌어오기 작업은 지정된 테이블에서만 레코드를 검색합니다. 동기화 컨텍스트에서 이 테이블에 대한 보류 중인 작업이 있으면 모바일 서비스 SDK에서 `PushAsync` 작업이 암시적으로 호출됩니다.
        
    ![][3] 



    ![][2]


  

##요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

로컬 저장소를 서버와 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync` 및 `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

*  변경 사항을 서버에 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`를 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.
   
* 서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`를 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다.  

    이 샘플에서는 쿼리 키와 쿼리를 지정할 수 있도록 하는 **PullAsync()**의 오버로드를 사용합니다. 쿼리 키는 증분 동기화에 사용됩니다. 모바일 서비스 SDK는 각각의 성공적인 끌어오기 작업 후 마지막으로 업데이트된 타임스탬프를 추적합니다. 다음 끌어오기 시 새 레코드만 검색됩니다. 쿼리 키를 지정하지 않은 경우 동기화 테이블에 대해 전체 동기화가 수행됩니다.

## 다음 단계

이 자습서의 완료된 버전은 [GitHub 샘플 리포지토리](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS)(영문)에서 다운로드할 수 있습니다.


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법]

<!-- Anchors. -->
[오프라인 기능을 지원하도록 앱 업데이트]: #enable-offline-app
[모바일 서비스에 연결된 앱 테스트]: #test-online-app
[다음 단계]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]: /ko-kr/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[데이터 작업 시작]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[모바일 서비스 시작]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법]: /ko-kr/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[모바일 서비스 SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite 저장소 nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Xamarin 확장]: http://xamarin.com/visual-studio
[Xamarin용 NuGet 추가 기능]: https://github.com/mrward/monodevelop-nuget-addin
