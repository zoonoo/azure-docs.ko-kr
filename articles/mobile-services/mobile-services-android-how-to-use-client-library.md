<properties
	pageTitle="모바일 서비스 Android 클라이언트 라이브러리 사용"
	description="Azure 모바일 서비스에 Android 클라이언트를 사용하는 방법에 대해 알아봅니다."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>


# 모바일 서비스용 Android 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [모바일 앱용 Android 클라이언트 라이브러리를 사용하는 방법](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md)을 참조하세요.
 
이 가이드에서는 Azure 모바일 서비스용 Android 클라이언트를 사용하여 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 데이터 쿼리, 삽입, 업데이트, 삭제 및 사용자 인증, 오류 처리 및 클라이언트 사용자 지정이 포함됩니다.

모바일 서비스를 처음 접하는 경우 먼저 [모바일 서비스 시작] 빠른 시작 자습서를 완료하는 것이 좋습니다. 이 자습서를 성공적으로 완료하면 Android Studio가 설치됩니다. 이를 통해 계정을 구성하고, 첫 번째 모바일 서비스를 만들고, 모바일 서비스 SDK(Android 버전 2.2 이상을 지원하지만 Android 버전 4.2 이상으로 기준으로 빌드하는 것이 좋음)를 설치할 수 있습니다.

Android 클라이언트 라이브러리용 Javadocs API 참조는 [여기](http://go.microsoft.com/fwlink/p/?LinkId=298735)에서 확인할 수 있습니다.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>설정 및 필수 조건

이미 모바일 서비스 및 테이블을 만들었다고 가정합니다. 자세한 내용은 [테이블 만들기](http://go.microsoft.com/fwlink/p/?LinkId=298592)를 참조하십시오. 이 항목에서 사용되는 코드에서 테이블 이름이 *ToDoItem*이고 테이블에 다음과 같은 열이 있다고 가정합니다.

- id
- text
- complete

해당 형식화된 클라이언트 쪽 개체는 다음과 같습니다.

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

동적 스키마가 사용하도록 설정된 경우 Azure 모바일 서비스에서 삽입 또는 업데이트 요청의 개체를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://go.microsoft.com/fwlink/p/?LinkId=296271)를 참조하십시오.

##<a name="create-client"></a>방법: 모바일 서비스 클라이언트 만들기
다음 코드는 모바일 서비스에 액세스하는 데 사용되는 [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 개체를 만듭니다. 이 코드는 *AndroidManifest.xml*에 지정된 Activity 클래스의 `onCreate` 메서드에 **MAIN** 작업 및 **LAUNCHER** 범주로 추가됩니다.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

위 코드에서 `MobileServiceUrl` 및 `AppKey`을(를) 모바일 서비스 URL 및 응용 프로그램 키로 바꿉니다(두 항목 순서 유지). 이 두 항목은 모두 Azure 클래식 포털에서 사용할 수 있습니다. 모바일 서비스를 선택한 후 *대시보드*를 클릭하면 됩니다.

##<a name="instantiating"></a>방법: 테이블 참조 만들기

모바일 서비스에서 데이터를 쿼리하거나 수정하는 가장 쉬운 방법은 *형식화된 프로그래밍 모델*을 사용하는 것입니다. Java는 강력한 형식의 언어이기 때문입니다(*형식화되지 않은* 모델에 대해서는 나중에 다룸). 이 모델은 클라이언트와 모바일 서비스 간에 데이터를 보낼 때 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 라이브러리를 사용하여 JSON에 대한 원활한 serialization 및 deserialization을 제공합니다. 개발자는 아무 작업도 수행할 필요가 없으며 프레임워크가 모든 과정을 처리해 줍니다.

데이터를 쿼리하거나 수정하기 위해 처음으로 하는 일은 [**MobileServiceClient**](http://go.microsoft.com/fwlink/p/?LinkId=296835)에 대해 [getTable](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) 메서드를 호출하여 **MobileServiceTable** 개체를 만드는 것입니다. 이 메서드의 오버로드 두 가지를 살펴보겠습니다.

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

다음 코드에서 *mClient*는 모바일 서비스 클라이언트에 대한 참조입니다.

[첫 번째 오버로드](http://go.microsoft.com/fwlink/p/?LinkId=296839)는 클래스 이름과 테이블 이름이 같을 때 사용합니다.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[두 번째 오버로드](http://go.microsoft.com/fwlink/p/?LinkId=296840)는 테이블 이름이 형식 이름과 다를 때 사용합니다.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>API 구조

클라이언트 라이브러리 버전 2.0부터 모바일 서비스 테이블 작업에서는 삽입, 업데이트, 삭제 등의 작업 및 쿼리와 관련된 메서드와 같은 모든 비동기 작업에서 [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) 및 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 개체를 사용합니다. 이를 통해 여러 중첩 콜백을 처리하지 않고도 여러 작업을 수행할 수 있습니다(백그라운드 스레드에 있는 동안).


##<a name="querying"></a>방법: 모바일 서비스에서 데이터 쿼리

이 섹션에서는 모바일 서비스에 대한 쿼리를 실행하는 방법을 설명합니다. 하위 섹션에서는 정렬, 필터링, 페이징과 같은 다양한 측면에 대해 설명합니다. 마지막으로 이 작업을 연결할 수 있는 방법을 알아봅니다.

### <a name="showAll"></a>방법: 테이블에서 모든 항목 반환

다음 코드는 *ToDoItem* 테이블의 모든 항목을 반환합니다. 어댑터에 항목을 추가하여 이를 UI에 표시합니다. 이 코드는 [모바일 서비스 시작] 빠른 시작 자습서에 있는 것과 유사합니다.

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


이와 같은 쿼리는 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 개체를 사용합니다.

*result* 변수는 쿼리에서 결과 집합을 반환하며, `mToDoTable.execute().get()` 문 뒤에 있는 코드는 개별 행을 표시하는 방법을 보여 줍니다.


### <a name="filtering"></a>방법: 반환된 데이터 필터링

다음 코드는 *ToDoItem* 테이블에서 *complete* 필드가 *false*와 일치하는 모든 항목을 반환합니다. *mToDoTable*은 이전에 만들어진 모바일 서비스 테이블에 대한 참조입니다.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



테이블 참조에 대한 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) 메서드 호출에서 필터를 시작합니다. 그 뒤에 [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) 메서드 호출, 논리 조건자를 지정하는 메서드 호출을 차례로 사용합니다. 가능한 조건자 메서드에는 [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466) 등이 포함됩니다.

이는 숫자 및 문자열 필드를 특정 값과 비교하기에 충분합니다. 하지만 훨씬 더 많은 기능을 사용할 수 있습니다.

예를 들어 날짜를 기준으로 필터링할 수 있습니다. 전체 날짜 필드를 비교할 수 있으며 [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471), [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472) 등의 메서드를 사용하여 날짜의 부분을 비교할 수도 있습니다. 다음 부분 코드는 *기한*이 2013과 같은 항목에 해당하는 필터를 추가합니다.

		mToDoTable.where().year("due").eq(2013).execute().get();

[**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495), [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496) 등의 메서드를 사용하여 문자열 필드에 다양한 복합 필터를 적용할 수 있습니다. 다음 부분 코드는 *text* 열이 "PRI0"으로 시작되는 테이블 행을 필터링합니다.

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

숫자 필드에도 [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506), [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507) 등의 메서드를 사용하여 다양한 복합 필터를 적용할 수 있습니다. 다음 부분 코드는 *duration*이 짝수인 테이블 행을 필터링합니다.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


조건자를 [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514), [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515) 등의 메서드와 결합할 수 있습니다. 다음 부분 코드는 위의 예 중 두 가지를 결합합니다.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

다음 부분 코드에서 볼 수 있는 것처럼 논리 연산자를 그룹화하고 중첩할 수 있습니다.

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

필터링에 대한 자세한 내용과 예를 보려면 [모바일 서비스 Android 클라이언트 쿼리 모델의 다양한 기능 알아보기](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)(영문)를 참조하십시오.

### <a name="sorting"></a>방법: 반환된 데이터 정렬

다음 코드는 *ToDoItems* 테이블에서 *text* 필드를 기준으로 오름차순 정렬된 모든 항목을 반환합니다. *mToDoTable*은 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

[**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) 메서드의 첫 번째 매개 변수는 정렬 기준이 되는 필드의 이름과 동일한 문자열입니다.

두 번째 매개 변수는 [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) 열거형을 사용하여 오름차순이나 내림차순으로 정렬할지를 지정합니다.

***where*** 메서드를 사용하여 필터링하려는 경우 ***orderBy*** 메서드 이전에 ***where*** 메서드를 호출해야 합니다.

### <a name="paging"></a>방법: 페이지에서 데이터 반환

첫 번째 예는 테이블에서 상위 5개 항목을 선택하는 방법을 보여 줍니다. 쿼리는 *ToDoItems* 테이블에서 항목을 반환합니다. *mToDoTable*은 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


다음으로, 첫 5 항목을 건너뛴 후 다음 5개를 반환하는 쿼리를 정의합니다.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>방법: 특정 열 선택

다음 코드는 *ToDoItems* 테이블에서 모든 항목을 반환하지만 *complete* 및 *text* 필드만 표시하는 방법을 보여 줍니다. *mToDoTable*은 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

		mToDoTable.select("complete", "text").execute().get();


여기서 select 함수의 매개 변수는 반환하려는 테이블 열의 문자열 이름입니다.

[**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) 메서드는 [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296), [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313) 등의 메서드(있는 경우) 뒤에 나와야 합니다. 그 뒤에 [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731) 등의 메서드가 나올 수 있습니다.

### <a name="chaining"></a>방법: 쿼리 메서드 연결

모바일 서비스 테이블을 쿼리하는 데 사용되는 메서드를 연결할 수 있습니다. 이를 통해 예를 들어 정렬 및 페이징되는 필터링된 행의 특정 열을 선택할 수 있습니다. 상당히 복잡한 논리 필터를 만들 수 있습니다.

사용하는 쿼리 메서드가 [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551) 개체를 반환하며 이 개체에 대해 호출되는 추가 메서드가 있을 수 있기 때문에 이런 작업이 가능합니다. 일련의 메서드를 종료하고 실제로 쿼리를 실행하려면 [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554) 메서드를 호출합니다.

다음은 *mToDoTable*이 모바일 서비스 *ToDoItem* 테이블에 대한 참조인 코드 샘플입니다.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

메서드를 연결하는 데 있어 기본적인 요구 사항은 *where* 메서드 및 조건자가 먼저 나와야 한다는 것입니다. 그 뒤에 응용 프로그램의 요구 사항에 가장 부합하는 순서로 후속 메서드를 호출할 수 있습니다.


##<a name="inserting"></a>방법: 모바일 서비스에 데이터 삽입

다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다.

먼저 *ToDoItem* 클래스 인스턴스를 인스턴스화하고 해당 속성을 설정합니다.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 그런 후 다음 코드를 실행합니다.

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


이 코드는 새 항목을 삽입하고 UI에 표시되도록 어댑터에 추가합니다.

모바일 서비스는 테이블의 ID 열에 대한 고유한 사용자 지정 문자열 값을 지원합니다. 이를 통해 응용 프로그램이 모바일 서비스 테이블의 ID 열에 대해 이메일 주소 또는 사용자 이름과 같은 사용자 지정 값을 사용할 수 있습니다. 예를 들어 각 레코드를 메일 주소로 식별하려는 경우 다음과 같은 JSON 개체를 사용합니다.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

새 레코드를 테이블에 삽입할 때 문자열 ID 값이 제공되지 않는 경우 모바일 서비스는 ID용으로 고유한 값을 생성합니다.

문자열 ID 지원은 개발자에게 다음과 같은 이점이 있습니다.

+ 데이터베이스에 대한 왕복 없이도 ID를 생성할 수 있습니다.
+ 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
+ 응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

서버 스크립트를 사용하여 ID 값을 설정할 수도 있습니다. 아래 스크립트 예제에서는 사용자 지정 GUID를 생성하여 새 레코드의 ID에 할당합니다. 이는 레코드의 ID 값을 전달하지 않은 경우 모바일 서비스가 생성하는 ID 값과 유사합니다.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


응용 프로그램에서 ID용 값을 제공하는 경우, 모바일 서비스는 값을 있는 그대로 저장합니다. 여기에는 선행 또는 후행 공백이 포함됩니다. 공백이 값에서 제거되지 않습니다.

`id`의 값은 고유해야 하며 다음과 같은 문자를 포함해서는 안 됩니다.

+ 제어 문자: [0x0000 0x001F] 및 [0x007F 0x009F]. 자세한 내용은 [ASCII 제어 코드 C0 및 C1](영문)을 참조하십시오.
+  인쇄 가능한 문자: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  ID "." 및 ".."

또는 테이블에 정수 ID를 사용할 수 있습니다. 정수 ID를 사용하려면 `mobile table create` 명령으로 `--integerId` 옵션을 사용하여 테이블을 만들어야 합니다. 이 명령은 Azure용 CLI(명령줄 인터페이스)와 함께 사용됩니다. CLI 사용에 대한 자세한 내용은 [모바일 서비스 테이블 관리 CLI]를 참조하세요.


##<a name="updating"></a>방법: 모바일 서비스의 데이터 업데이트

다음 코드는 테이블의 데이터를 업데이트하는 방법을 보여 줍니다. 이 예제에서 *item*은 일부 변경된 *ToDoItem* 테이블의 행에 대한 참조입니다. 다음 메서드는 테이블 및 UI 어댑터를 업데이트합니다.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>방법: 모바일 서비스의 데이터 삭제

다음 코드는 테이블에서 데이터를 삭제하는 방법을 보여 줍니다. UI에서 **Completed** 확인란이 선택된 기존 항목을 ToDoItem 테이블에서 삭제합니다.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


다음 코드는 이를 수행하는 다른 방법을 보여 줍니다. 이 코드는 삭제할 행의 id 필드 값을 지정하여("2FA404AB-E458-44CD-BC1B-3BC847EF0902"와 동일하다고 가정함) ToDoItem 테이블의 기존 항목을 삭제합니다. 실제 앱에서는 ID를 선택하여 변수로 전달합니다. 여기서는 테스트를 간소화하기 위해 Azure 클래식 포털의 서비스로 이동하여 **데이터**를 클릭하고 테스트할 ID를 복사할 수 있습니다.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>방법: 특정 항목 조회
일반적으로 일부 조건을 충족하는 항목 컬렉션을 가져오는 쿼리와 달리, *id*를 기준으로 특정 항목을 조회하려는 경우가 있습니다. 다음 코드는 이 작업을 수행하는 방법을 보여 줍니다(*id* 값은 `0380BAFB-BCFF-443C-B7D5-30199F730335`). 실제 앱에서는 ID를 선택하여 변수로 전달합니다. 여기서는 테스트를 간소화하기 위해 Azure 클래식 포털의 서비스로 이동하여 **데이터**를 클릭하고 테스트할 ID를 복사할 수 있습니다.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>방법: 형식화되지 않은 데이터 작업

형식화되지 않은 프로그래밍 모델에서는 JSON serialization을 정확히 제어할 수 있으며, 이 모델을 사용할 수 있는 일부 시나리오가 있습니다. 예를 들어 모바일 서비스 테이블에 포함된 다수의 열 중에서 몇 개만 참조해야 하는 경우를 들 수 있습니다. 형식화된 모델을 사용하려면 모바일 서비스 테이블의 모든 열을 데이터 클래스에서 정의해야 합니다. 하지만 형식화되지 않은 모델에서는 사용해야 하는 열만 정의합니다.

데이터에 액세스하는 대부분의 API 호출은 형식화된 프로그래밍 호출과 유사합니다. 주요 차이점은 형식화되지 않은 모델에서는 **MobileServiceTable** 개체 대신 **MobileServiceJsonTable** 개체에 대해 메서드를 호출한다는 점입니다.


### <a name="json_instance"></a>방법: 형식화되지 않은 테이블 인스턴스 만들기

형식화된 모델과 유사하게 테이블 참조를 가져와서 시작합니다. 하지만 이 경우에는 이 참조가 [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733) 개체입니다. 모바일 서비스 클라이언트 인스턴스에 대해 [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) 메서드를 호출하여 참조를 가져옵니다.

먼저 변수를 정의합니다.

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



**onCreate** 메서드에서 모바일 서비스 클라이언트 인스턴스(여기서는 *mClient* 변수)를 만든 후에는 다음 코드를 사용하여 **MobileServiceJsonTable** 인스턴스를 만듭니다.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

**MobileServiceJsonTable** 인스턴스를 만들고 나면 형식화된 프로그래밍 모델에서 할 수 있는 거의 모든 메서드를 이 인스턴스에 대해 호출할 수 있습니다. 하지만 일부 경우 메서드는 다음 예처럼 형식화되지 않은 매개 변수를 사용합니다.

### <a name="json_insert"></a>방법: 형식화되지 않은 테이블에 삽입

다음 코드는 삽입하는 방법을 보여 줍니다. 첫 번째 단계는 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 라이브러리의 일부인 [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html)를 만드는 것입니다.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

다음 단계는 개체를 삽입하는 것입니다. [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) 메서드에 전달되는 콜백 함수는 [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532) 클래스의 인스턴스입니다. *insert* 메서드의 매개 변수는 JsonObject입니다.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


삽입된 개체의 ID를 가져와야 하는 경우 다음 메서드 호출을 사용합니다.

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>방법: 형식화되지 않은 테이블에서 삭제

다음 코드는 인스턴스를 삭제하는 방법을 보여 줍니다. 이 경우에는 앞의 **insert** 예에서 만들어진 것과 동일한 *JsonObject* 인스턴스를 삭제합니다. 코드는 형식화된 경우와 같지만 **JsonObject**를 참조하기 때문에 메서드의 서명이 다릅니다.


         mToDoTable.delete(item);


ID를 사용하여 직접 인스턴스를 삭제할 수도 있습니다.

		 mToDoTable.delete(ID);



### <a name="json_get"></a>방법: 형식화되지 않은 테이블에서 모든 행 반환

다음 코드는 전체 테이블을 검색하는 방법을 보여 줍니다. Json 테이블을 사용하기 때문에 테이블 열의 일부만 선택적으로 검색할 수 있습니다.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

형식화된 프로그래밍 모델에 사용되는 것과 같은 이름을 가진 메서드를 연결하여 필터링, 정렬, 페이징할 수 있습니다.


##<a name="binding"></a>방법: 사용자 인터페이스에 데이터 바인딩

데이터 바인딩에는 세 가지 구성 요소가 필요합니다.

- 데이터 원본
- 화면 레이아웃
- 두 요소를 연결하는 어댑터

샘플 코드에서는 모바일 서비스 테이블 *ToDoItem*의 데이터를 배열로 반환합니다. 이것은 데이터 응용 프로그램의 가장 일반적인 한 가지 패턴입니다. 데이터베이스 쿼리가 일반적으로 클라이언트에서 목록 또는 배열로 가져오는 행 컬렉션을 반환하는 것입니다. 이 샘플에서 배열은 데이터 원본입니다.

코드는 장치에 나타나는 데이터 뷰를 정의하는 화면 레이아웃을 지정합니다.

그리고 이 두 요소는 어댑터(*ArrayAdapter&lt;ToDoItem&gt;* 클래스의 확장)를 통해 바인딩됩니다.

### <a name="layout"></a>방법: 레이아웃 정의

레이아웃은 다수의 XML 코드 조각으로 정의됩니다. 기존 레이아웃을 고려할 때 다음 코드는 서버 데이터로 채울 **ListView**를 나타낸다고 가정하겠습니다.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


위의 코드에서 *listitem* 특성은 목록의 개별 행에 대한 레이아웃의 ID를 지정합니다. 다음은 확인란 및 관련 텍스트를 지정하는 코드입니다. 이 코드는 목록의 항목별로 한 번씩 인스턴스화됩니다. 더 복잡한 레이아웃은 디스플레이의 추가 필드를 지정합니다. 이 코드는 *row\_list\_to\_do.xml* 파일에 있습니다.

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:orientation="horizontal">
	    <CheckBox
	        android:id="@+id/checkToDoItem"
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:text="@string/checkbox_text" />
	</LinearLayout>


### <a name="adapter"></a>방법: 어댑터 정의

이 뷰의 데이터 원본은 *ToDoItem*의 배열이기 때문에, *ArrayAdapter&lt;ToDoItem&gt;* 클래스에서 어댑터의 서브클래스를 지정합니다. 이 서브클래스는 *row\_list\_to\_do* 레이아웃을 사용하는 모든 *ToDoItem*의 뷰를 생성합니다.

이 예제 코드에서 *ArrayAdapter&lt;E&gt;* 클래스의 확장인 다음 클래스를 정의합니다.

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


어댑터의 *getView* 메서드를 다시 정의해야 합니다. 이 샘플 코드는 이 작업을 수행하는 방법의 한 예로, 세부 사항은 사용하는 응용 프로그램에 따라 다릅니다.

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

		return row;
	}

작업에서 다음과 같이 이 클래스의 인스턴스를 만듭니다.

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter 생성자의 두 번째 매개 변수는 레이아웃에 대한 참조입니다. 생성자 호출 뒤에 다음 코드가 나옵니다. 다음 코드는 먼저 **ListView**에 대한 참조를 가져온 후 *setAdapter*를 호출하여 방금 만든 어댑터를 사용하도록 자체 구성합니다.

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>방법: 어댑터 사용

이제 데이터 바인딩을 사용할 준비가 되었습니다. 다음 코드는 모바일 서비스 테이블의 항목을 가져오고, 어댑터를 비운 후, 어댑터의 *add* 메서드를 호출하여 반환된 항목으로 어댑터를 채우는 방법을 보여 줍니다.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

또한 *ToDoItem* 테이블을 수정할 때 그 결과를 표시하려면 언제든지 어댑터를 호출해야 합니다. 수정은 레코드별로 이루어지기 때문에 컬렉션이 아니라 단일 행을 다루게 됩니다. 항목을 삽입할 때는 어댑터에 대해 *add* 메서드를 호출하고, 삭제할 때는 *remove* 메서드를 호출합니다.

##<a name="custom-api"></a>방법: 사용자 지정 API 호출

사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다. 모바일 서비스에서 사용자 지정 API를 만드는 방법의 예는 [방법: 사용자 지정 API 끝점 정의](mobile-services-dotnet-backend-define-custom-api.md)를 참조하세요.

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>방법: 사용자 인증

모바일 서비스는 Facebook, Google, Microsoft 계정, Twitter 및 Azure Active Directory와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작](http://go.microsoft.com/fwlink/p/?LinkId=296316)(영문)을 참조하십시오.

두 가지의 인증 흐름, 즉 *서버* 흐름과 *클라이언트* 흐름이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하므로 Single Sign-On과 같은 장치 특정 기능을 통해 깊이 있는 통합이 가능합니다.

앱에서 인증을 사용하도록 설정하려면 세 가지 단계가 필요합니다.

- 인증을 위해 공급자에 앱 등록 및 모바일 서비스 구성
- 테이블 사용 권한을 인증된 사용자로만 제한
- 앱에 인증 코드 추가


모바일 서비스는 사용자를 인증하는 데 사용할 수 있는 다음과 같은 기존 ID 공급자를 지원합니다.

- Microsoft 계정
- Facebook
- Twitter
- Google
- Azure Active Directory

테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 요청을 수정할 수도 있습니다.

처음 두 가지 작업은 [Azure 클래식 포털](https://manage.windowsazure.com/)을 사용하여 수행합니다. 자세한 내용은 [인증 시작](http://go.microsoft.com/fwlink/p/?LinkId=296316)(영문)을 참조하십시오.

### <a name="caching"></a>방법: 앱에 인증 코드 추가

1.  앱의 활동 파일에 다음 import 문을 추가합니다.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. 활동 클래스의 **onCreate** 메서드에서 `MobileServiceClient` 개체를 만드는 코드 뒤에 다음 코드 줄을 추가합니다. `MobileServiceClient` 개체에 대한 참조가 *mClient*라고 간주합니다.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    이 코드는 Google 로그인을 사용하는 사용자를 인증합니다. 인증된 사용자의 ID를 표시하는 대화 상자가 나타납니다. 양성 인증 없이는 진행할 수 없습니다.

    > [AZURE.NOTE] Google 이외의 ID 공급자를 사용하는 경우, 위의 **login** 메서드에 전달된 값을 다음 중 하나로 변경합니다. _MicrosoftAccount_, _Facebook_, _Twitter_ 또는 _WindowsAzureActiveDirectory_


3. 앱을 실행할 때는 선택한 ID 공급자로 로그인합니다.


### <a name="caching"></a>방법: 인증 토큰 캐시

이 섹션에서는 인증 토큰을 캐시하는 방법을 보여 줍니다. 인증 토큰을 캐시하면 토큰의 유효성이 유지되는 동안 앱이 오랫동안 사용되지 않은 경우 사용자가 다시 인증받을 필요가 없습니다.

인증 토큰을 캐시하려면 사용자 ID 및 인증 토큰을 장치에 로컬로 저장해야 합니다. 다음에 앱이 시작될 때 캐시를 확인하여 이 값이 있는 경우 로그인 절차를 건너뛰고 이 데이터로 클라이언트를 리하이드레이션할 수 있습니다. 하지만 이 데이터는 중요하므로 휴대폰을 분실하는 경우 안전을 위해 암호화하여 저장해야 합니다.

다음 코드 조각은 Microsoft 계정 로그인을 위한 토큰을 획득하는 방법을 보여줍니다. 토큰이 캐시되고 캐시가 발견되는 경우 다시 로드됩니다.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
	}


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined");
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined");
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


토큰이 만료되면 어떻게 될까요? 이 경우에는 연결하기 위해 토큰을 사용하려고 하면 *401 권한 없음* 응답을 받습니다. 그러면 사용자는 로그인하여 새 토큰을 획득해야 합니다. 모바일 서비스 호출 및 모바일 서비스에서 나오는 응답을 차단할 수 있게 하는 필터를 사용하면 앱에서 모바일 서비스를 호출하는 모든 위치에서 이를 처리하는 코드를 작성할 필요가 없습니다. 필터 코드가 401의 응답을 테스트하고 필요한 경우 로그인 프로세스를 트리거한 후 401을 생성한 요청을 다시 시작합니다.


##<a name="customizing"></a>방법: 클라이언트 사용자 지정

모바일 서비스 클라이언트의 기본 동작을 사용자 지정할 수 있는 여러 가지 방법이 있습니다.

### <a name="headers"></a>방법: 요청 헤더 사용자 지정

모든 보내는 요청에 사용자 지정 헤더를 추가할 수 있습니다. 다음과 같이 **ServiceFilter**를 구성하면 됩니다.

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>방법: serialization 사용자 지정

모바일 서비스에서는 기본적으로 서버의 테이블 이름, 열 이름, 데이터 형식이 모두 클라이언트와 정확히 일치한다고 가정합니다. 하지만 서버와 클라이언트의 이름이 일치하지 않는 이유가 있을 수 있습니다. 한 가지 예로 경쟁 제품 대신 모바일 서비스를 사용하도록 기존 클라이언트를 변경하려는 경우를 들 수 있습니다.

다음과 같은 종류의 사용자 지정을 수행할 수 있습니다.

- 모바일 서비스 테이블에서 사용되는 열 이름이 클라이언트에서 사용 중인 이름과 일치하지 않음
- 클라이언트에서 매핑되는 클래스와 다른 이름을 가진 모바일 서비스 테이블 사용
- 속성 자동 대문자 표시 설정
- 개체에 복합 속성 추가

### <a name="columns"></a>방법: 다른 클라이언트 및 서버 이름 매핑

Java 클라이언트 코드에서 *ToDoItem* 개체 속성에 다음과 같이 표준 Java 스타일 이름이 사용된다고 가정하겠습니다.

- mId
- mText
- mComplete
- mDuration


클라이언트 이름을 서버에 있는 *ToDoItem* 테이블의 열 이름과 일치하는 JSON 이름으로 직렬화해야 합니다. 다음 코드는 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 라이브러리를 이용하여 이를 수행합니다.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>방법: 클라이언트 및 모바일 서비스 간에 다른 테이블 이름 매핑

클라이언트 테이블 이름을 다른 모바일 서비스 테이블 이름에 매핑하는 작업은 쉽습니다. 다음 코드에 나온 것처럼 <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 함수의 재정의 중 하나를 사용하면 됩니다.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>방법: 열 이름 매핑 자동화

열이 몇 개뿐인 작은 테이블의 열 이름을 매핑하는 일은 이전 섹션에서 보았듯이 어렵지 않습니다. 하지만 테이블 20개 또는 30개의 많은 열이 있을 수 있습니다. <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> API를 호출하고 모든 열에 적용될 변환 전략을 지정하여 모든 단일 열 이름을 주석 처리할 필요가 없다는 사실이 확인되었습니다.

이렇게 하려면 Android 클라이언트 라이브러리리에서 자동으로 Java 개체를 JSON 데이터에 직렬화하여 Azure 모바일 서비스로 전송하는 데 사용하는 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 라이브러리를 사용합니다.

다음 코드에서는 *setFieldNamingStrategy()* 메서드가 사용되며, 이 메서드에서 *FieldNamingStrategy()* 메서드를 정의합니다. 이 메서드는 시작 문자("m")를 삭제한 후 각 필드 이름에 대해 다음 문자를 소문자로 처리하도록 지시합니다. 이 코드는 또한 출력 JSON이 보기 쉽게 인쇄되도록 합니다.

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());



이 코드는 모바일 서비스 클라이언트 개체에 대한 모든 메서드 호출 이전에 실행해야 합니다.

### <a name="complex"></a>방법: 테이블에 개체 또는 배열 속성 저장

지금까지 모든 serialization 예에는 정수 및 JSON뿐만 아니라 모바일 서비스 테이블에도 쉽게 직렬화되는 문자열과 같은 기본 형식이 사용되었습니다. JSON 및 테이블에 자동으로 직렬화되지 않는 클라이언트 유형에 복합 개체를 추가하려는 경우를 가정하겠습니다. 예를 들어 클라이언트 개체에 문자열 배열을 추가할 수 있습니다. 이제 serialization 수행 방법 및 모바일 서비스 테이블에 배열을 저장하는 방법을 지정하면 됩니다.

이를 수행하는 방법의 예를 보려면 블로그 게시물인 <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">모바일 서비스 Android 클라이언트에서 <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> 라이브러리를 사용하여 serialization 사용자 지정</a>을 확인하십시오.

이 일반적인 방법은 자동으로 JSON 및 모바일 서비스 테이블에 직렬화할 수 없는 복합 개체가 있을 때마다 사용할 수 있습니다.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[모바일 서비스 시작]: mobile-services-android-get-started.md
[ASCII 제어 코드 C0 및 C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!---HONumber=AcomDC_0727_2016-->