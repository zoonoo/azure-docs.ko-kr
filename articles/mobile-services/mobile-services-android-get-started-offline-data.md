<properties
	pageTitle="Android 모바일 서비스 앱에 오프라인 데이터 동기화 추가 | Microsoft Azure"
	description="Azure 모바일 서비스를 사용하여 Android 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다."
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="ricksal"/>

# Android 모바일 서비스 앱에 오프라인 데이터 동기화 추가

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## 요약

서비스 없이 영역으로 이동할 때나 네트워크 문제로 인해 모바일 앱에서 네트워크 연결이 손실될 수 있습니다. 예를 들어 원격 건축 현장에서 사용되는 건축 산업 앱에서는 나중에 Azure로 동기화되는 일정 데이터를 입력해야 할 수 있습니다. Azure 모바일 서비스 오프라인 동기화를 통해 대부분 모바일 앱에 필수적인 네트워크 연결이 손실될 때도 계속 작업할 수 있습니다. 오프라인 동기화를 통해 Azure SQL Server 테이블의 로컬 복사본을 사용하고 두 항목을 주기적으로 다시 동기화합니다.

이 자습서에서는 [모바일 서비스 빠른 시작 자습서]에서 앱을 업데이트하여 오프라인 동기화를 사용하도록 설정하고 데이터를 오프라인으로 추가하고, 해당 항목을 온라인 데이터베이스에 동기화하고, Azure 클래식 포털에서 변경 내용을 확인하여 앱을 테스트합니다.

오프라인 또는 연결 상태인지와 관계없이 데이터에 여러 변경 내용이 적용되면 언제든지 충돌이 발생할 수 있습니다. 이후 자습서에서는 적용할 변경 내용 버전을 선택하는 동기화 충돌 처리를 살펴봅니다. 이 자습서에서는 동기화 충돌이 없다고 가정하고 기존 데이터에 대한 모든 변경이 직접 Azure SQL Server에 적용됩니다.


## 시작에 필요한 항목

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## 오프라인 동기화를 지원하도록 앱 업데이트

오프라인 동기화를 통해 장치의 **SQL Light** 데이터베이스 일부인 *동기화 테이블*에서 읽고 씁니다(*IMobileServiceSyncTable* 인터페이스 사용).

장치와 Azure 모바일 서비스 간에 변경 내용을 푸시하거나 끌어오려면 데이터를 로컬로 저장하는 데 사용할 로컬 데이터베이스와 함께 초기화하는 *동기화 컨텍스트*(*MobileServiceClient.SyncContext*)를 사용합니다.

1. 이 코드를 *AndroidManifest.xml* 파일에 추가하여 네트워크 연결을 확인할 권한을 추가합니다.

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. 다음 **import** 문을 *ToDoActivity.java*에 추가합니다.

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. `ToDoActivity` 클래스 위쪽 근처에서 `mToDoTable` 변수 선언을 `MobileServiceTable<ToDoItem>` 클래스에서 `MobileServiceSyncTable<ToDoItem>` 클래스로 변경합니다.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	여기에서 동기화 테이블을 정의합니다.

4. 로컬 저장소의 초기화를 처리하려면 `onCreate` 메서드에서 `MobileServiceClient` 인스턴스를 만들고 나서 다음 줄을 추가합니다.

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. `try` 블록 내부에 있는 이전 코드에 이어 `MalformedURLException` 블록 뒤에 다른 `catch` 블록을 추가합니다.

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. 이 메서드를 추가하여 네트워크 연결을 확인합니다.

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. 이 메서드를 추가하여 로컬 *SQL Light* 저장소와 Azure SQL Server 간에 동기화합니다.

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. `onCreate` 메서드에서 `refreshItemsFromTable`에 대한 호출 바로 앞에 이 코드를 마지막 다음 줄로 추가합니다.

			syncAsync();

	이렇게 하면 시작 시 장치가 Azure 테이블과 동기화됩니다. 그렇지 않으면 로컬 저장소의 마지막 오프라인 콘텐츠가 표시됩니다.



9. 이 쿼리를 사용하도록 `refreshItemsFromTable` 메서드에서 코드를 업데이트합니다(`try` 블록 내부의 첫 번째 코드 줄).

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. `onOptionsItemSelected` 메서드에서 `if` 블록의 콘텐츠를 이 코드로 바꿉니다.

			syncAsync();
			refreshItemsFromTable();

	이 코드는 오른쪽 위에서 **새로 고침** 단추를 누를 때 실행됩니다. 이 방법은 로컬 저장소를 Azure에 동기화하고 시작 시 동기화하는 기본 방법입니다. 이 방법에서는 동기화 변경 내용의 일괄 처리를 권장하고 이 방법은 Azure에서 끌어오기가 비교적 비싼 작업에서 효율적입니다. 앱에 필요할 경우 `addItem` 및 `checkItem` 메서드에 `syncAsync`에 대한 호출을 추가하여 변경될 때마다 동기화되도록 이 앱을 디자인할 수도 있습니다.


## 앱 테스트

이 섹션에서는 WiFi가 켜졌을 때의 동작을 테스트하고 WiFi를 끄고 오프라인 시나리오를 만듭니다.

데이터 항목을 추가하면 **새로 고침** 단추를 누를 때까지 모바일 서비스에 동기화되지 않고 로컬 SQL Light 저장소에 보관됩니다. 앱에 따라 데이터를 동기화해야 하는 경우에 대한 요구 사항이 다르지만 데모 목적으로 이 자습서에는 사용자가 명시적으로 요청합니다.

해당 단추를 누르면 새 백그라운드 작업이 시작되고, 먼저 로컬 저장소에 대한 모든 변경 내용이 동기화 컨텍스트를 통해 푸시되고 나서 모든 변경된 데이터를 Azure에서 로컬 테이블로 끌어옵니다.


### 온라인 테스트

다음 시나리오를 테스트해 보세요.

1. 장치에서 몇 가지 새 항목을 추가하고,
2. 항목이 포털에 표시되지 않는지 확인하고,
3. 다음으로 **새로 고침**을 눌러서 항목이 표시되는지 확인합니다.
4. 포털에서 항목을 변경하거나 추가하고 **새로 고침**을 눌러서 변경 내용이 장치에 나타나는지 확인합니다.

### 오프라인 테스트

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. 장치 또는 시뮬레이터를 *비행기 모드*로 전환합니다. 이렇게 하면 오프라인 시나리오가 생성됩니다.

2. 몇몇 *ToDo* 항목을 추가하거나 몇몇 항목을 완료로 표시합니다. 장치 또는 시뮬레이터를 종료하거나 앱을 강제로 닫고 다시 시작합니다. 변경 내용은 로컬 SQL Light 저장소에 보관되므로 변경 내용이 장치에서 지속되었는지 확인합니다.

3. Azure *TodoItem* 테이블의 콘텐츠를 표시합니다. 새 항목이 서버와 동기화되지 _않았는지_ 확인합니다.

   - JavaScript 백 엔드의 경우 Azure 클래식 포털로 가서 데이터 탭을 클릭하여 `TodoItem` 테이블의 콘텐츠를 봅니다.
   - .NET 백 엔드의 경우 *SQL Server Management Studio*와 같은 SQL 도구나 *Fiddler* 또는 *Postman*과 같은 REST 클라이언트를 사용하여 테이블 콘텐츠를 봅니다.

4. 장치 또는 시뮬레이터에서 WiFi를 켭니다. 다음으로 **새로 고침** 단추를 누릅니다.

5. Azure 클래식 포털에서 TodoItem 데이터를 다시 봅니다. 이제 새 및 변경된 TodoItems가 나타납니다.


## 다음 단계

* [모바일 서비스에서 일시 삭제 사용][Soft Delete]

## 추가 리소스

* [클라우드 커버: Azure 모바일 서비스의 오프라인 동기화]

* [Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱](참고: 데모는 Windows용이지만 기능 설명은 모든 플랫폼에 적용)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[클라우드 커버: Azure 모바일 서비스의 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[모바일 서비스 빠른 시작 자습서]: mobile-services-android-get-started.md

<!---HONumber=AcomDC_1203_2015-->