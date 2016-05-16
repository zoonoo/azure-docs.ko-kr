<properties
	pageTitle="Azure 모바일 앱(Android)용 오프라인 동기화 사용"
	description="앱 서비스 모바일 앱을 사용하여 Android 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다."
	documentationCenter="android"
	authors="lindydonna"
	manager="erikre"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="donnam;ricksal"/>

# Android 모바일 앱에 대해 오프라인 동기화 사용

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 개요

이 자습서에서는 Android용 Azure 모바일 앱의 오프라인 동기화 기능을 설명합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 백 엔드와 동기화됩니다.

Azure 모바일 앱을 처음 사용하는 경우, 먼저 [Android 앱 만들기] 자습서를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화] 항목을 참조하세요.

## 오프라인 동기화를 지원하도록 앱 업데이트

오프라인 동기화를 통해 장치의 **SQLite** 데이터베이스 일부인 *동기화 테이블*에서 읽고 씁니다(*IMobileServiceSyncTable* 인터페이스 사용).

장치와 Azure 모바일 서비스 간에 변경 내용을 푸시하거나 끌어오려면 데이터를 로컬로 저장하는 데 사용할 로컬 데이터베이스와 함께 초기화하는 *동기화 컨텍스트*(*MobileServiceClient.SyncContext*)를 사용합니다.

1. `TodoActivity.java`에서 `mToDoTable`의 기존 정의를 주석으로 처리하고 동기화 테이블 버전의 주석 처리를 제거합니다.

	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. `onCreate` 메서드에서 `mToDoTable`의 기존 초기화를 주석으로 처리하고 이 정의의 주석 처리를 제거합니다.

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. `refreshItemsFromTable`에서 `results`의 정의를 주석으로 처리하고 이 정의의 주석 처리를 제거합니다.

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. `refreshItemsFromMobileServiceTable` 정의를 주석으로 처리합니다.

5. `refreshItemsFromMobileServiceTableSyncTable` 정의의 주석 처리를 제거합니다.

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. `sync` 정의의 주석 처리를 제거합니다.

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## 앱 테스트

이 섹션에서는 WiFi가 켜졌을 때의 동작을 테스트하고 WiFi를 끄고 오프라인 시나리오를 만듭니다.

데이터 항목을 추가하면 **새로 고침** 단추를 누를 때까지 모바일 서비스에 동기화되지 않고 로컬 SQLite 저장소에 보관됩니다. 앱에 따라 데이터를 동기화해야 하는 경우에 대한 요구 사항이 다르지만 데모 목적으로 이 자습서에는 사용자가 명시적으로 요청합니다.

해당 단추를 누르면 새 백그라운드 작업이 시작되고, 먼저 로컬 저장소에 대한 모든 변경 내용이 동기화 컨텍스트를 통해 푸시되고 나서 모든 변경된 데이터를 Azure에서 로컬 테이블로 끌어옵니다.

### 오프라인 테스트

1. 장치 또는 시뮬레이터를 *비행기 모드*로 전환합니다. 이렇게 하면 오프라인 시나리오가 생성됩니다.

2. 몇몇 *ToDo* 항목을 추가하거나 몇몇 항목을 완료로 표시합니다. 장치 또는 시뮬레이터를 종료하거나 앱을 강제로 닫고 다시 시작합니다. 변경 내용은 로컬 SQLite 저장소에 보관되므로 변경 내용이 장치에서 지속되었는지 확인합니다.

3. *SQL Server Management Studio*와 같은 SQL 도구나 *Fiddler* 또는 *Postman*과 같은 REST 클라이언트를 사용하여 Azure *TodoItem* 테이블의 내용을 봅니다. 새 항목이 서버와 동기화되지 _않았는지_ 확인합니다.

   	+ Node.js 백 엔드의 경우 [Azure 포털](https://portal.azure.com/)로 이동하여 모바일 앱 백 엔드에서 **Easy Tables**(쉬운 테이블) > **TodoItem**을 클릭하여 `TodoItem` 테이블의 내용을 봅니다.
   	+ .NET 백 엔드의 경우 *SQL Server Management Studio*와 같은 SQL 도구나 *Fiddler* 또는 *Postman*과 같은 REST 클라이언트를 사용하여 테이블 내용을 봅니다.

4. 장치 또는 시뮬레이터에서 WiFi를 켭니다. 다음으로 **새로 고침** 단추를 누릅니다.

5. TodoItem 데이터를 Azure 포털에 다시 표시합니다. 이제 새 및 변경된 TodoItems가 나타납니다.

## 추가 리소스

* [Azure 모바일 앱에서 오프라인 데이터 동기화]

* [Cloud Cover: Azure 모바일 서비스에서 오프라인 동기화](참고: 비디오는 모바일 서비스에 있지만 Azure 모바일 앱에서 비슷한 방식으로 오프라인 동기화가 작동합니다.)


<!-- URLs. -->

[Azure 모바일 앱에서 오프라인 데이터 동기화]: ../app-service-mobile-offline-data-sync.md

[Android 앱 만들기]: ../app-service-mobile-android-get-started.md

[Cloud Cover: Azure 모바일 서비스에서 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0504_2016-->