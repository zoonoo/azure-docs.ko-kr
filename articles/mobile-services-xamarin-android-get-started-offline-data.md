<properties linkid="develop-mobile-tutorials-get-started-offline-data-android" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc"></tags>

# 모바일 서비스에서 오프라인 데이터 동기화 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko--kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows 스토어 C#">Windows 스토어 C#</a>
<a href="/ko--kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ko--kr/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용하는 방법을 보여 줍니다. 이러한 기능을 사용하면 모바일 서비스의 오프라인 시나리오의 경우 로컬 데이터베이스를 조작할 수 있습니다. 온라인으로 다시 전환되면 오프라인 기능을 사용하여 로컬 변경을 모바일 서비스와 동기화할 수 있습니다.

이 자습서에서는 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서에서 작성한 앱을 업데이트하여 Azure 모바일 서비스의 오프라인 기능을 지원합니다. 그다음 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고, 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

> [WACOM.NOTE] 이 자습서는 Windows 스토어 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 퀵 스타트에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [오프라인 기능을 지원하도록 앱 업데이트][오프라인 기능을 지원하도록 앱 업데이트]
2.  [모바일 서비스에 연결된 앱 테스트][모바일 서비스에 연결된 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

-   [Xamarin 확장][Xamarin 확장]이 포함된 Visual Studio **또는** [Xamarin Studio][Xamarin Studio]
-   [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서 완료
-   [Azure 모바일 서비스 SDK 버전 1.3.0-alpha3][Azure 모바일 서비스 SDK 버전 1.3.0-alpha3]
-   [Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-alpha2][Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-alpha2]

> [WACOM.NOTE] 아래의 지침에서는 Xamarin 확장이 포함된 Visual Studio 2012 이상을 사용 중이라고 가정합니다. Xamarin Studio를 사용할 경우 대부분의 지침이 동일하지만 시험판 모바일 서비스 NeGet 패키지를 프로젝트에 쉽게 추가할 수 있도록 [Xamarin용 NuGet 추가 기능][Xamarin용 NuGet 추가 기능]을 설치해야 합니다.

## <a name="enable-offline-app"></a>오프라인 기능을 지원하도록 앱 업데이트

Azure 모바일 서비스의 오프라인 기능을 사용하면 오프라인에서 모바일 서비스를 사용하여 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화합니다. 그런 다음 `IMobileServiceSyncTable` 인터페이스를 통해 테이블을 참조합니다.

1.  Visual Studio에서 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서에서 완료한 프로젝트를 엽니다. 솔루션 탐색기의 **구성 요소**에서 **Azure 모바일 서비스 SDK**에 대한 참조를 제거합니다.

2.  패키지 관리자 콘솔에서 다음 명령을 사용하여 모바일 서비스 SQLiteStore의 시험판 패키지를 설치합니다.

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    그러면 모든 필요한 종속 항목도 설치됩니다.

3.  참조 노드에서 `System.IO`, `System.Runtime` 및 `System.Threading.Tasks`에 대한 참조를 제거합니다.

### ToDoActivity.cs 편집

-   선언 추가

        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using System.IO;

-   `ToDoActivity.toDoTable` 멤버 유형을 `IMobileServiceTable<>`에서 `IMobileServiceSyncTable<>`로 변경

-   `OnCreate(Bundle)` 메서드에서 `client` 멤버를 초기화하는 행 뒤에 다음 코드를 추가합니다.

        // existing initializer
        client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);

        // new code to initialize the SQLite store
        string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");

        if (!File.Exists(path))
        {
            File.Create(path).Dispose();
        }

        var store = new MobileServiceSQLiteStore(path);
        store.DefineTable<ToDoItem>();

        await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

-   동일한 메서드에서 `GetTable<>` 대신 `GetSyncTable<>` 메서드를 사용하도록 `toDoTable`을 초기화하는 행을 변경합니다.

        toDoTable = client.GetSyncTable <ToDoItem> ();

-   `PushAsync` 및 `PullAsync`에 대한 호출을 추가하도록 `OnRefreshItemsSelected` 메서드를 수정합니다.

        async void OnRefreshItemsSelected ()
        {
            await client.SyncContext.PushAsync();
            await toDoTable.PullAsync();
            await RefreshItemsFromTableAsync();
        }

### ToDoItem.cs 편집

-   using 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices; 

-   다음 멤버를 `ToDoItem` 클래스에 추가합니다.

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

## <a name="test-online-app"></a>앱 테스트

이 섹션에서는 로컬 저장소를 모바일 서비스 데이터베이스와 동기화하는 `SyncAsync` 메서드를 테스트합니다.

1.  Visual Studio에서 **실행** 단추를 눌러 프로젝트를 빌드하고 이 프로젝트의 기본값인 iPhone 에뮬레이터에서 앱을 시작합니다.

2.  앱의 항목 목록은 비어 있습니다. 이전 섹션의 코드 변경으로 인해 앱이 더 이상 모바일 서비스에서 항목을 읽지 않지만, 로컬 저장소에서는 항목을 읽습니다.

3.  ToDo 목록에 항목 추가

    ![][]

4.  Microsoft Azure 관리 포털에 로그인하고 데이터베이스에서 모바일 서비스를 찾습니다. 서비스에서 모바일 서비스에 대해 JavaScript 백 엔드를 사용하는 경우 모바일 서비스의 **데이터** 탭에서 데이터를 찾아볼 수 있습니다. 모바일 서비스에 대해 .NET 백 엔드를 사용 중인 경우 SQL Azure 확장 프로그램에서 데이터베이스의 **관리** 단추를 클릭하여 테이블에 대한 쿼리를 실행할 수 있습니다.

    데이터베이스와 로컬 저장소의 데이터는 아직 동기화되어 있지 않습니다.

5.  앱에서 **새로 고침** 단추를 누릅니다. 그러면 앱이 `MobileServiceClient.SyncContext.PushAsync`, `IMobileServiceSyncTable.PullAsync()` 및 `RefreshTodoItems`를 차례로 호출하여 로컬 저장소에 있는 항목으로 앱을 새로 고칩니다.

    푸시 작업은 모바일 서비스 데이터베이스에서 저장소의 데이터를 받습니다. 이 작업은 `IMobileServicesSyncTable` 대신 `MobileServiceClient.SyncContext`에서 실행되며 해당 동기화 컨텍스트에 연결된 모든 테이블에서 변경 사항을 푸시합니다. 그러면 테이블 간에 관계가 존재하는 시나리오가 포함됩니다.

    반대로, 끌어오기 작업은 지정된 테이블에서만 레코드를 검색합니다. 동기화 컨텍스트에서 이 테이블에 대한 보류 중인 작업이 있으면 모바일 서비스 SDK에서 `PushAsync` 작업이 암시적으로 호출됩니다.

    ![][1]

    ![][2]

## 요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `IMobileServiceSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MobileServiceClient.SyncContext`를 초기화했습니다. 이 경우 로컬 저장소는 SQLite 데이터베이스입니다.

모바일 서비스에 대한 일반 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `IMobileServiceSyncTable.PullAsync`, `MobileServiceClient.SyncContext.PushAsync` 메서드를 사용했습니다.

-   서버의 변경 내용을 푸시하기 위해 `IMobileServiceSyncContext.PushAsync()`를 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `IMobileServicesSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

-   서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `IMobileServiceSyncTable.PullAsync`를 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다.

    또한 쿼리를 지정하도록 허용하는 **PullAsync()** 오버로드가 있습니다. 모바일 서비스에 대한 오프라인 지원 미리 보기 릴리스에서 **PullAsync**는 해당 테이블 또는 쿼리에서 모든 행을 읽습니다. 예를 들어 마지막 동기화보다 최신 상태인 행만 읽으려고 시도하지 않습니다. 행이 로컬 동기화 테이블에 이미 있는 경우 해당 행은 변경되지 않습니다.

## 다음 단계

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법][Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [Windows 스토어 C#]: /ko--kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows 스토어 C#"
  [Windows Phone]: /ko--kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /ko--kr/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /ko--kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /ko--kr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [모바일 서비스 시작]: /ko--kr/documentation/articles/partner-xamarin-mobile-services-android-get-started/
  [데이터 시작]: /ko--kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
  [Azure 무료 평가판]: http://www.windowsazure.com/ko--kr/pricing/free-trial/?WT.mc_id=AE564AB28
  [오프라인 기능을 지원하도록 앱 업데이트]: #enable-offline-app
  [모바일 서비스에 연결된 앱 테스트]: #test-online-app
  [Xamarin 확장]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Azure 모바일 서비스 SDK 버전 1.3.0-alpha3]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha3
  [Azure 모바일 서비스 SQLite 저장소 버전 1.0.0-alpha2]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha2
  [Xamarin용 NuGet 추가 기능]: https://github.com/mrward/monodevelop-nuget-addin
  []: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
  [1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png
  [2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
  [Azure 모바일 서비스용 Xamarin Component 클라이언트를 사용하는 방법]: /ko--kr/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
