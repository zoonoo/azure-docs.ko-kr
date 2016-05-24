<properties
	pageTitle="Android 모바일 앱 클라이언트 라이브러리를 사용하는 방법"
	description="Azure 모바일 앱에 Android 클라이언트 SDK를 사용하는 방법"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="ricksal"/>


# 모바일 앱용 Android 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 데이터에 대한 쿼리(삽입, 업데이트 및 삭제), 사용자 인증, 오류 처리 및 클라이언트 사용자 지정과 같은 일반적인 시나리오를 구현하는 모바일 앱용 Android 클라이언트 SDK를 사용하는 방법을 보여줍니다. 또한 대부분의 모바일 앱에서 사용하는 일반적인 클라이언트 코드에 심층 분석을 수행합니다.

이 가이드는 클라이언트 쪽 Android SDK에 중점을 둡니다. 모바일 앱용 서버 쪽 SDK에 대해 자세히 알아보려면 [.NET 백 엔드 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 또는 [Node.js 백 엔드 SDK를 사용하는 방법](app-service-mobile-node-backend-how-to-use-server-sdk.md)을 참조하세요.

## 참조 설명서

Android 클라이언트 라이브러리용 Javadocs API 참조는 [GitHub](http://azure.github.io/azure-mobile-apps-android-client/)에서 확인할 수 있습니다.

## 설정 및 필수 조건

Android용 모바일 서비스 SDK는 Android 버전 2.2 이상을 지원하지만 버전 4.2 이상에 대해 빌드하는 것이 좋습니다.

[모바일 앱 퀵 스타트](app-service-mobile-android-get-started.md) 자습서를 완료합니다. 자습서를 완료하려면 Android Studio를 반드시 설치해야 하며, 이는 계정을 구성하고 첫 번째 모바일 앱 백 엔드를 만드는 데 도움이 됩니다. 이 작업을 수행하는 경우 이 섹션의 나머지 부분을 건너뛸 수 있습니다.

퀵 스타트 자습서를 완료하지 않고 Android 앱을 모바일 앱 백엔드에 연결하려는 경우 다음을 수행해야 합니다.

- Android 앱과 함께 사용할 [모바일 앱 백 엔드를 만듭니다](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend)(아직 앱에 모바일 앱 백 엔드가 없는 경우).
- Android Studio에서 [Gradle 빌드 파일을 업데이트](#gradle-build)하고
- [인터넷 권한을 사용합니다.](#enable-internet)

그런 다음 심층 분석 섹션에 설명한 단계를 완료해야 합니다.

###<a name="gradle-build"></a>Gradle 빌드 파일 업데이트

**build.gradle** 파일을 모두 변경합니다.

1. *buildscript* 태그 내의 *프로젝트* 수준 **build.gradle** 파일에 이 코드를 추가합니다.

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. *dependencies* 태그 내의 *모듈 앱* 수준 **build.gradle** 파일에 이 코드를 추가합니다.

		compile 'com.microsoft.azure:azure-mobile-android:3.1'

	현재 최신 버전은 3.1입니다. 지원되는 버전은 [여기](http://go.microsoft.com/fwlink/p/?LinkID=717034)에 나열됩니다.

###<a name="enable-internet"></a>인터넷 권한 사용
Azure에 액세스하려면 앱은 인터넷 사용 권한을 사용하도록 설정해야 합니다. 아직 사용하지 않는 경우 코드의 다음 줄을 **AndroidManifest.xml** 파일에 추가합니다.

	<uses-permission android:name="android.permission.INTERNET" />

## 기본 사항 자세히 알아보기

이 섹션은 퀵 스타트 앱의 코드 중 일부를 설명합니다. 퀵 스타트를 완료하지 않은 경우 앱에 이 코드를 추가해야 합니다.

> [AZURE.NOTE] "MobileServices" 문자열은 코드에서 자주 발생합니다. 코드는 모바일 앱 SDK를 실제로 참조하고 과거에서 가져온 임시 결과일 뿐입니다.


###<a name="data-object"></a>클라이언트 데이터 클래스 정의

SQL Azure 테이블에서 데이터에 액세스하려면 모바일 앱 백 엔드에 있는 테이블에 해당하는 클라이언트 데이터 클래스를 정의합니다. 이 토픽의 예에서는 이름이 *ToDoItem*이고 다음 열이 포함된 테이블을 가정합니다.

- id
- text
- complete

해당하는 형식화된 클라이언트 쪽 개체는 다음과 같습니다.

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

코드는 **ToDoItem.java**라는 파일에 상주합니다.

SQL Azure 테이블이 더 많은 열을 포함하는 경우 이 클래스에 해당하는 필드를 추가합니다.

예를 들어 정수 우선 순위 열이 있다면 getter 및 setter 메서드와 함께 이 필드를 추가할 수 있습니다.

		private Integer priority;

	    /**
	     * Returns the item priority
	     */
	    public Integer getPriority() {
	        return mPriority;
	    }

	    /**
	     * Sets the item priority
	     *
	     * @param priority
	     *            priority to set
	     */
	    public final void setPriority(Integer priority) {
	        mPriority = priority;
	    }

모바일 앱 백 엔드에 추가 테이블을 작성하는 방법을 알아보려면 [방법: 테이블 컨트롤러 정의](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller)(.NET 백 엔드) 또는 [동적 스키마를 사용하여 테이블 정의](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)(Node.js 백 엔드)를 참조하세요. 또한 Node.js 백 엔드의 경우 [Azure 포털]에서 **쉬운 테이블** 설정을 사용할 수 있습니다.

###<a name="create-client"></a>방법: 클라이언트 컨텍스트 만들기

다음 코드는 모바일 앱 백 엔드에 액세스하는 데 사용되는 **MobileServiceClient** 개체를 만듭니다. 이 코드는 *AndroidManifest.xml*에 지정된 **작업** 클래스의 `onCreate` 메서드에 **기본** 작업 및 **시작 관리자** 범주로 추가됩니다. 빠른 시작 코드에서 **ToDoActivity.java** 파일로 이동합니다.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileAppUrl", // Replace with the above Site URL
				this)

위의 코드에서 `MobileAppUrl`을 모바일 앱 백 엔드의 URL로 대체하며 이는 모바일 앱 백 엔드에 대한 블레이드의 [Azure 포털](https://portal.azure.com/)에서 찾을 수 있습니다. 또한 컴파일할 코드의 줄의 경우 다음 **가져오기** 문을 추가해야 합니다.

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>방법: 테이블 참조 만들기

백 엔드에서 데이터를 쿼리하거나 수정하는 가장 쉬운 방법은 *형식화된 프로그래밍 모델*을 사용하는 것입니다. Java는 강력한 형식의 언어이기 때문입니다(*형식화되지 않은* 모델에 대해서는 나중에 다룸). 이 모델은 백 엔드 Azure SQL에서 클라이언트 개체와 테이블 간에 데이터를 보낼 때 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 라이브러리를 사용하여 원활한 JSON 직렬화 및 역직렬화를 제공합니다. 개발자는 아무 작업도 수행할 필요가 없으며 프레임워크가 모든 과정을 처리해 줍니다.

테이블에 액세스하려면 우선 [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html)에서 **getTable** 메서드를 호출하여 [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) 개체를 만듭니다. 이 메서드에는 두 가지 오버로드가 있습니다.

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

다음 코드에서 *mClient*는 MobileServiceClient 개체에 대한 참조입니다.

[첫 번째 오버로드](http://go.microsoft.com/fwlink/p/?LinkId=296839)는 클래스 이름과 테이블 이름이 같을 때 사용하며 빠른 시작에서 사용되는 것입니다.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[두 번째 오버로드](http://go.microsoft.com/fwlink/p/?LinkId=296840)는 테이블 이름이 형식 이름과 다를 때 사용하고 첫 번째 매개 변수는 테이블 이름입니다.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>방법: 사용자 인터페이스에 데이터 바인딩

데이터 바인딩에는 세 가지 구성 요소가 필요합니다.

- 데이터 원본
- 화면 레이아웃
- 두 요소를 연결하는 어댑터

샘플 코드에서는 모바일 앱 SQL Azure 테이블 *ToDoItem*의 데이터를 배열로 반환합니다. 데이터 응용 프로그램의 가장 일반적인 패턴입니다. 데이터베이스 쿼리가 대체로 클라이언트에서 목록 또는 배열로 가져오는 행 컬렉션을 반환하는 것입니다. 이 샘플에서 배열은 데이터 원본입니다.

코드는 장치에 나타나는 데이터 뷰를 정의하는 화면 레이아웃을 지정합니다.

그리고 이 두 요소는 어댑터(*ArrayAdapter&lt;ToDoItem&gt;* 클래스의 확장)를 통해 바인딩됩니다.

#### <a name="layout"></a>방법: 레이아웃 정의

레이아웃은 다수의 XML 코드 조각으로 정의됩니다. 기존 레이아웃을 고려할 때 다음 코드는 서버 데이터로 채울 **ListView**를 나타낸다고 가정하겠습니다.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>


위의 코드에서 *listitem* 특성은 목록의 개별 행에 대한 레이아웃의 ID를 지정합니다. 다음은 확인란 및 관련 텍스트를 지정하는 코드입니다. 이 코드는 목록의 항목별로 한 번씩 인스턴스화됩니다. 이 레이아웃은 **ID** 필드를 표시하지 않고 더 복잡한 레이아웃은 디스플레이에서 추가 필드를 지정합니다. 이 코드는 **row\_list\_to\_do.xml** 파일에 있습니다.

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


#### <a name="adapter"></a>방법: 어댑터 정의

이 뷰의 데이터 원본은 *ToDoItem*의 배열이기 때문에, *ArrayAdapter&lt;ToDoItem&gt;* 클래스에서 어댑터의 서브클래스를 지정합니다. 이 서브클래스는 *row\_list\_to\_do* 레이아웃을 사용하는 모든 *ToDoItem*의 뷰를 생성합니다.

이 예제 코드에서 *ArrayAdapter&lt;E&gt;* 클래스의 확장인 다음 클래스를 정의합니다.

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


어댑터의 *getView* 메서드를 다시 정의해야 합니다. 이 샘플 코드는 이 작업을 수행하는 방법의 한 예로, 세부 사항은 사용하는 응용 프로그램에 따라 다릅니다.

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

작업에서 다음과 같이 이 클래스의 인스턴스를 만듭니다.

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter 생성자의 두 번째 매개 변수는 레이아웃에 대한 참조입니다. 생성자 호출 뒤에 다음 코드가 나옵니다. 다음 코드는 먼저 **ListView**에 대한 참조를 가져온 후 *setAdapter*를 호출하여 방금 만든 어댑터를 사용하도록 자체 구성합니다.

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API 구조

모바일 앱 테이블 작업 및 사용자 지정 API 호출은 비동기적이므로 [미래](http://developer.android.com/reference/java/util/concurrent/Future.html) 및 [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) 개체를 쿼리 및 삽입, 업데이트 및 삭제를 비롯한 모든 비동기 메서드에서 사용합니다. 이를 통해 여러 중첩 콜백을 처리하지 않고도 백그라운드 스레드에서 여러 작업을 수행할 수 있습니다.

이러한 비동기 API가 Android 앱에서 사용되는 방법 및 데이터가 UI에 표시되는 방법을 확인하려면 [Azure 포털]에서 Android 빠른 시작 프로젝트의 **ToDoActivity.java** 파일을 검토합니다.


#### <a name="use-adapter"></a>방법: 어댑터 사용

이제 데이터 바인딩을 사용할 준비가 되었습니다. 다음 코드는 모바일 서비스 테이블의 항목을 가져오고, 어댑터를 비운 후, 어댑터의 *추가* 메서드를 호출하여 반환된 항목으로 어댑터를 채우는 방법을 보여 줍니다.


**TBD**:이 코드를 테스트합니다.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

또한 *ToDoItem* 테이블을 수정할 때 그 결과를 표시하려면 언제든지 어댑터를 호출해야 합니다. 수정은 레코드별로 이루어지기 때문에 컬렉션이 아니라 단일 행을 다루게 됩니다. 항목을 삽입할 때는 어댑터에 대해 *add* 메서드를 호출하고, 삭제할 때는 *remove* 메서드를 호출합니다.

##<a name="querying"></a>방법: 모바일 앱 백 엔드에서 데이터 쿼리

이 섹션에서는 다음 작업을 비롯하여 모바일 앱 백 엔드에 대한 쿼리를 실행하는 방법을 설명합니다.

- [모든 항목 반환]
- [반환된 데이터 필터링]
- [반환된 데이터 정렬]
- [페이지에서 데이터 반환]
- [특정 열 선택]
- [쿼리 메서드 연결](#chaining)

### <a name="showAll"></a>방법: 테이블에서 모든 항목 반환

다음 쿼리는 *ToDoItem* 테이블의 모든 항목을 반환합니다.

	List<ToDoItem> results = mToDoTable.execute().get();

*결과* 변수는 쿼리 결과 집합을 목록으로 반환합니다.

### <a name="filtering"></a>방법: 반환된 데이터 필터링

다음 쿼리 실행은 *완료*가 *false*와 같은 *ToDoItem* 테이블에서 모든 항목을 반환합니다. 빠른 시작에 이미 있는 코드입니다.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable*은 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

테이블 참조에 대한 **where** 메서드 호출을 사용하여 필터를 정의합니다. 그 뒤에 **필드** 메서드 호출, 논리 조건자를 지정하는 메서드 호출을 차례로 사용합니다. 가능한 조건자 메서드는 **eq**(같음) **ne**(같지 않음), **gt**(보다 큼), **ge**(보다 크거나 같음), **lt**(보다 작음), **le**(작거나 같음) 등을 포함합니다. 이러한 메서드를 사용하면 숫자 및 문자열 필드를 특정 값과 비교할 수 있습니다.

날짜를 기준으로 필터링할 수 있습니다. 다음 메서드를 사용하면 전체 날짜 필드 또는 날짜의 부분을 비교할 수 있습니다. **년**, **달**, **일**, **시간**, **분** 및 **초**. 다음 예제는 *기한*이 2013과 같은 항목에 해당하는 필터를 추가합니다.

	mToDoTable.where().year("due").eq(2013).execute().get();

**startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**, **length**와 같은 메서드는 문자열 필드에 복합 필터를 지원합니다. 다음 예제는 *텍스트* 열이 "PRI0"으로 시작되는 테이블 행을 필터링합니다.

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

다음 연산자 메서드는 숫자 필드에 지원됩니다. **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** 및 **round**. 다음 예제는 *기간*이 짝수인 테이블 행을 필터링합니다.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

조건자를 **and**, **or**, **not** 등의 논리 메서드와 결합할 수 있습니다. 다음 예제는 위의 예 중 두 가지를 결합합니다.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

다음과 같이 논리 연산자를 그룹화하고 중첩할 수 있습니다.

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

필터링에 대한 자세한 내용과 예를 보려면 [Android 클라이언트 쿼리 모델의 다양한 기능 알아보기](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)를 참조하세요.

### <a name="sorting"></a>방법: 반환된 데이터 정렬

다음 코드는 *ToDoItems* 테이블에서 *텍스트* 필드를 기준으로 오름차순 정렬된 모든 항목을 반환합니다. *mToDoTable*은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** 메서드의 첫 번째 매개 변수는 정렬 기준이 되는 필드의 이름과 동일한 문자열입니다.

두 번째 매개 변수는 **QueryOrder** 열거형을 사용하여 오름차순이나 내림차순으로 정렬할지를 지정합니다.

***where*** 메서드를 사용하여 필터링하려는 경우 ***orderBy*** 메서드 이전에 ***where*** 메서드를 호출해야 합니다.

### <a name="paging"></a>방법: 페이지에서 데이터 반환

첫 번째 예는 테이블에서 상위 5개 항목을 선택하는 방법을 보여 줍니다. 쿼리는 *ToDoItems* 테이블에서 항목을 반환합니다. *mToDoTable*은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


다음은 첫 5 항목을 건너뛴 후 다음 5개를 반환하는 쿼리입니다.

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>방법: 특정 열 선택

다음 코드는 *ToDoItems* 테이블에서 모든 항목을 반환하지만 *완료* 및 *텍스트* 필드만 표시하는 방법을 보여 줍니다. *mToDoTable*은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


여기서 select 함수의 매개 변수는 반환하려는 테이블 열의 문자열 이름입니다.

**select** 메서드는 **where** 및 **orderBy** 등의 메서드가 있는 경우 그 뒤에 나와야 합니다. 그 뒤에 **top** 등의 메서드를 페이징하여 나올 수 있습니다.

### <a name="chaining"></a>방법: 쿼리 메서드 연결

확인한 대로 백 엔드 테이블을 쿼리하는 데 사용되는 메서드를 연결할 수 있습니다. 이를 통해 예를 들어 정렬 및 페이징되는 필터링된 행의 특정 열을 선택할 수 있습니다. 상당히 복잡한 논리 필터를 만들 수 있습니다.

사용하는 쿼리 메서드가 **MobileServiceQuery&lt;T&gt;** 개체를 반환하며 이 개체에 대해 호출되는 추가 메서드가 있을 수 있기 때문에 이런 작업이 가능합니다. 일련의 메서드를 종료하고 실제로 쿼리를 실행하려면 **실행** 메서드를 호출합니다.

다음은 *mToDoTable*이 *ToDoItem* 테이블에 대한 참조인 코드 샘플입니다.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

메서드를 연결하는 데 있어 기본적인 요구 사항은 *where* 메서드 및 조건자가 먼저 나와야 한다는 것입니다. 그 뒤에 응용 프로그램의 요구 사항에 가장 부합하는 순서로 후속 메서드를 호출할 수 있습니다.


##<a name="inserting"></a>방법: 백 엔드에 데이터 삽입

다음 코드는 테이블에 새 행을 삽입하는 방법을 보여 줍니다.

먼저 *ToDoItem* 클래스 인스턴스를 인스턴스화하고 해당 속성을 설정합니다.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

그런 후 다음 코드를 실행합니다.

	ToDoItem entity = mToDoTable.insert(item).get();

반환된 엔터티는 백 엔드 테이블에 삽입된 데이터와 일치하며 이는 ID 및 백 엔드에 설정된 다른 값을 포함합니다.

모바일 앱은 각 테이블에 **ID**라는 열이 필요하며 이는 테이블을 인덱스하는 데 사용됩니다. 기본적으로 이 열은 오프라인 동기화를 지원해야 하는 문자열 데이터 형식입니다. ID 열의 기본값은 GUID이지만 전자 메일 주소 또는 사용자 이름과 같은 다른 고유 값을 제공할 수 있습니다. 삽입된 레코드에 대한 문자열 ID 값을 제공하는 경우 백 엔드는 새 GUID 값을 생성합니다.

문자열 ID 값은 다음과 같은 이점을 제공합니다.

+ 데이터베이스에 대한 왕복 없이도 ID를 생성할 수 있습니다.
+ 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
+ 응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합됩니다.

##<a name="updating"></a>방법: 모바일 앱의 데이터 업데이트

다음 코드는 테이블의 데이터를 업데이트하는 방법을 보여 줍니다.

    mToDoTable.update(item).get();

이 예제에서 *item*은 일부 변경된 *ToDoItem* 테이블의 행에 대한 참조입니다.

##<a name="deleting"></a>방법: 모바일 앱의 데이터 삭제

다음 코드는 데이터 개체를 지정하여 테이블에서 데이터를 삭제하는 방법을 보여줍니다.

	mToDoTable.delete(item);

또한 삭제할 행의 **ID** 필드를 지정하여 항목을 삭제할 수 있습니다.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>방법: 특정 항목 조회

이 코드는 특정 *ID*로 항목을 조회하는 방법을 보여줍니다.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>방법: 형식화되지 않은 데이터 작업

형식화되지 않은 프로그래밍 모델에서는 JSON serialization을 정확히 제어할 수 있으며, 이 모델을 사용할 수 있는 일부 시나리오가 있습니다. 예를 들어 백 엔드 테이블에 포함된 다수의 열 중에서 몇 개만 참조해야 하는 경우를 들 수 있습니다. 형식화된 모델을 사용하려면 모바일 앱 테이블의 모든 열을 데이터 클래스에서 정의해야 합니다. 하지만 형식화되지 않은 모델에서는 사용해야 하는 열만 정의합니다.

데이터에 액세스하는 대부분의 API 호출은 형식화된 프로그래밍 호출과 유사합니다. 주요 차이점은 형식화되지 않은 모델에서는 **MobileServiceTable** 개체 대신 **MobileServiceJsonTable** 개체에 대해 메서드를 호출한다는 점입니다.


### <a name="json_instance"></a>방법: 형식화되지 않은 테이블 인스턴스 만들기

형식화된 모델과 유사하게 테이블 참조를 가져와서 시작합니다. 하지만 이 경우에는 이 참조가 **MobileServicesJsonTable** 개체입니다. 클라이언트의 인스턴스에 대해 **getTable** 메서드를 호출하여 참조를 가져옵니다.

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

**MobileServiceJsonTable** 인스턴스를 만들고 나면 형식화된 프로그래밍 모델에서 할 수 있는 거의 모든 메서드를 이 인스턴스에 대해 호출할 수 있습니다. 하지만 일부 경우 메서드는 다음 예처럼 형식화되지 않은 매개 변수를 사용합니다.

### <a name="json_insert"></a>방법: 형식화되지 않은 테이블에 삽입

다음 코드는 삽입하는 방법을 보여 줍니다. 첫 번째 단계는 [gson](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) 라이브러리의 일부인 **[JsonObject](http://go.microsoft.com/fwlink/p/?LinkId=290801)**를 만드는 것입니다.

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

다음 단계는 개체를 삽입하는 것입니다.

    mJsonToDoTable.insert(jsonItem).get();


삽입된 개체의 ID를 가져와야 하는 경우 다음 메서드 호출을 사용합니다.

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>방법: 형식화되지 않은 테이블에서 삭제

다음 코드는 인스턴스를 삭제하는 방법을 보여 줍니다. 이 경우에는 앞의 **insert** 예에서 만들어진 것과 동일한 *JsonObject* 인스턴스를 삭제합니다. 코드는 형식화된 경우와 같지만 **JsonObject**를 참조하기 때문에 메서드의 서명이 다릅니다.


         mToDoTable.delete(jsonItem);


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



##<a name="custom-api"></a>방법: 사용자 지정 API 호출

사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

Android 클라이언트에서 **invokeApi** 메서드를 호출하여 사용자 지정 API 끝점을 호출합니다. 다음 예제에서는 *completeAll*이라는 API 끝점을 호출하는 방법을 보여주며 이는 MarkAllResult라는 컬렉션 클래스를 반환합니다.

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

**invokeApi** 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다. 다른 버전의 **invokeApi**를 사용하면 필요에 따라 요청 본문에 개체를 보내고 HTTP 메서드를 지정하며 요청으로 쿼리 매개 변수를 보낼 수 있습니다. **invokeApi**의 형식화되지 않은 버전도 제공됩니다.

##<a name="authentication"></a>방법: 앱에 인증 추가

자습서는 이러한 기능을 추가하는 방법을 이미 자세히 설명합니다.

앱 서비스는 Facebook, Google, Microsoft 계정, Twitter 및 Azure Active Directory와 같이 다양한 외부 ID 공급자를 사용하여 [앱 사용자의 인증](app-service-mobile-android-get-started-users.md)을 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다.

두 가지의 인증 흐름, 즉 *서버* 흐름과 *클라이언트* 흐름이 지원됩니다. 서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 장치별 SDK를 사용하고 이를 코딩하도록 하므로 Single Sign-On과 같은 장치 특정 기능을 통해 심도 깊은 통합이 가능합니다.

앱에서 인증을 사용하도록 설정하려면 세 가지 단계가 필요합니다.

- 인증을 위해 공급자를 사용하여 앱 등록 및 모바일 앱 백 엔드 구성
- 테이블 사용 권한을 인증된 사용자로만 제한
- 앱에 인증 코드 추가

테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 SID를 사용하여 요청을 수정할 수도 있습니다.

처음 두 가지 작업은 [Azure 포털](https://portal.azure.com/)을 사용하여 수행합니다. 자세한 내용은 [인증 시작](영문)을 참조하십시오.

### <a name="caching"></a>방법: 앱에 인증 코드 추가

다음 코드는 Google 공급자를 사용하여 서버 흐름 로그인 프로세스를 시작합니다.

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

**getUserId** 메서드를 사용하여 **MobileServiceUser**에서 로그인한 사용자의 ID를 가져올 수 있습니다. Futures를 사용하여 비동기 로그인 API를 호출하는 방법의 예제는 [인증 시작]을 참조하세요.


### <a name="caching"></a>방법: 인증 토큰 캐시

인증 토큰을 캐시하려면 사용자 ID 및 인증 토큰을 장치에 로컬로 저장해야 합니다. 다음에 앱이 시작될 때 캐시를 확인하여 이 값이 있는 경우 로그인 절차를 건너뛰고 이 데이터로 클라이언트를 리하이드레이션할 수 있습니다. 하지만 이 데이터는 중요하므로 휴대폰을 분실하는 경우 안전을 위해 암호화하여 저장해야 합니다.

인증 토큰을 캐시하는 방법의 완전한 예제를 [캐시 인증 토큰 섹션](app-service-mobile-android-get-started-users.md#cache-tokens)에서 볼 수 있습니다.

만료된 토큰을 사용하려고 하면 *401 권한 없음* 응답을 받습니다. 그러면 사용자는 로그인하여 새 토큰을 획득해야 합니다. 모바일 서비스 호출 및 모바일 서비스에서 나오는 응답을 차단할 수 있게 하는 필터를 사용하면 앱에서 모바일 서비스를 호출하는 모든 위치에서 이를 처리하는 코드를 작성할 필요가 없습니다. 필터 코드가 401의 응답을 테스트하고 필요한 경우 로그인 프로세스를 트리거한 후 401을 생성한 요청을 다시 시작합니다. 또한 토큰을 검사하여 만료를 확인할 수 있습니다.


## <a name="adal"></a>방법: Active Directory 인증 라이브러리를 사용하여 사용자 인증

Azure Active Directory를 사용하여 응용 프로그램에 사용자가 로그인하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다. `loginAsync()` 메서드는 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기에 해당 메서드 사용을 선호할 수 있습니다.

1. 다음으로 [Active Directory 로그인에 앱 서비스를 구성하는 방법](app-service-mobile-how-to-configure-active-directory-authentication.md) 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 응용 프로그램을 등록하는 선택적 단계를 완료해야 합니다.

2. 다음을 포함하는 build.gradle 파일을 수정하여 ADAL을 설치합니다.

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. 응용 프로그램에 아래 코드를 추가하며 이는 다음의 대체 작업을 수행합니다.

* **INSERT-AUTHORITY-HERE**를 응용 프로그램이 프로비전된 테넌트의 이름으로 바꿉니다. https://login.windows.net/contoso.onmicrosoft.com 형식이어야 합니다. 이 값은 [Azure 클래식 포털]의 Azure Active Directory에 있는 도메인 탭에서 복사될 수 있습니다.

* **INSERT-RESOURCE-ID-HERE**를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 **Azure Active Directory 설정**에 있는 **고급**에서 이를 가져올 수 있습니다.

* **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

* HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE**를 사이트의 _/.auth/login/done_ 끝점으로 바꿉니다. 이 값은 \__https://contoso.azurewebsites.net/.auth/login/done_와 유사해야 합니다.

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## 방법: 앱에 푸시 알림 추가

Microsoft Azure 알림 허브가 다양한 푸시 알림을 지원하는 방법을 설명하는 [개요를 읽을](notification-hubs-overview.md/#integration-with-app-service-mobile-apps) 수 있습니다.

[이 자습서](app-service-mobile-android-get-started-push.md)에서 레코드가 삽입될 때 마다 푸시 알림이 전송됩니다.

## 방법: 앱에 오프라인 동기화 추가
빠른 시작 자습서는 오프라인 동기화를 구현하는 코드를 포함합니다. 다음과 같은 주석이 접두사인 코드를 찾습니다.

	// Offline Sync

다음 코드 줄의 주석 처리를 제거하여 오프라인 동기화를 구현할 수 있고 다른 모바일 앱 코드에 비슷한 코드를 추가할 수 있습니다.

##<a name="customizing"></a>방법: 클라이언트 사용자 지정

클라이언트의 기본 동작을 사용자 지정할 수 있는 여러 가지 방법이 있습니다.

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

클라이언트는 백 엔드의 테이블 이름, 열 이름 및 데이터 형식이 모두 클라이언트에 정의된 데이터 개체와 일치한다고 가정합니다. 하지만 서버와 클라이언트의 이름이 일치하지 않는 이유가 있을 수 있습니다. 시나리오에서 다음과 같은 종류의 사용자 지정을 수행할 수 있습니다.

- 모바일 서비스 테이블에서 사용되는 열 이름이 클라이언트에서 사용 중인 이름과 일치하지 않습니다.
- 클라이언트에서 매핑되는 클래스와 다른 이름을 가진 모바일 서비스 테이블을 사용합니다.
- 속성 자동 대문자 표시를 설정합니다.
- 개체에 복합 속성을 추가합니다.

### <a name="columns"></a>방법: 다른 클라이언트 및 서버 이름 매핑

Java 클라이언트 코드에서 *ToDoItem* 개체 속성에 다음과 같이 표준 Java 스타일 이름이 사용된다고 가정하겠습니다.

- mId
- mText
- mComplete
- mDuration


클라이언트 이름을 서버에 있는 *ToDoItem* 테이블의 열 이름과 일치하는 JSON 이름으로 직렬화해야 합니다. 다음 코드는 [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) 라이브러리를 이용하여 이를 수행합니다.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>방법: 클라이언트와 백 엔드 간에 다른 테이블 이름 매핑

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
[모든 항목 반환]: #showAll
[반환된 데이터 필터링]: #filtering
[반환된 데이터 정렬]: #sorting
[페이지에서 데이터 반환]: #paging
[특정 열 선택]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure 포털]: https://portal.azure.com
[인증 시작]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0511_2016-->