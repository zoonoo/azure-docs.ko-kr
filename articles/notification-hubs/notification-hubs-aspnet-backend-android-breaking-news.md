<properties 
	pageTitle="알림 허브 속보 자습서 - Android" 
	description="Azure 서비스 버스 알림 허브를 사용하여 Android 장치에 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="ricksal"/>


# 알림 허브를 사용하여 속보 보내기
<div class="dev-center-tutorial-selector sublanding">     	
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

이 항목에서는 Azure 알림 허브를 사용하여 Android 앱에 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 완료하면, 관심이 있는 속보 범주를 등록하고 해당 범주의 푸시 알림만 받을 수 있습니다. 이 시나리오는 RSS 수집기, 음악 애호가를 위한 앱 등 이전에 관심을 보인 사용자 그룹에 알림을 보내야 하는 많은 앱에 공통된 패턴입니다. 

브로드캐스트 시나리오를 사용하려면 알림 허브에서 등록을 만들 때 하나 이상의 _태그_를 포함하면 됩니다. 태그에 알림이 전송되면 태그에 대해 등록된 모든 장치에서 알림을 받게 됩니다. 태그는 단순히 문자열이므로 사전에 프로비전해야 할 필요가 없습니다. 태그에 대한 자세한 내용은 [알림 허브 지침]을 참조하세요. 

이 자습서에서는 이 시나리오를 사용하기 위한 다음 기본 단계를 차례로 안내합니다.

1. [앱에 범주 선택 추가]
2. [알림 등록]
3. [백 엔드에서 알림 보내기]
4. [앱 실행 및 알림 생성]

이 항목은 [알림 허브 시작][get-started]에서 만든 앱을 기반으로 작성되었습니다. 이 자습서를 시작하기 전에 먼저 [알림 허브 시작][get-started]을 완료해야 합니다.

##<a name="adding-categories"></a>앱에 범주 선택 추가

첫 번째 단계는 기존의 기본 활동에 사용자가 등록할 범주를 선택할 수 있도록 하는 UI 요소를 추가하는 것입니다. 사용자가 선택한 범주는 장치에 저장됩니다. 앱을 시작하면 장치 등록이 선택한 범주와 함께 태그로서 알림 허브에 생성됩니다. 

1. res/layout/activity_main.xml 파일을 열고 콘텐츠를 다음으로 대체합니다.
			
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">
		
		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. res/values/strings.xml 파일을 열고 다음 줄을 추가합니다.

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	main_activity.xml 그래픽 레이아웃은 다음과 같이 표시되어야 합니다.

	![][A1]

3. 이제 **MainActivity** 클래스와 같은 패키지에서 **Notifications** 클래스를 만듭니다.

		import java.util.HashSet;
		import java.util.Set;
		
		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;
		
		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;		
		
		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;
			
			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;
				
				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}
			
			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}
			
			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}
		
					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}
			
		}

	이 클래스는 로컬 저장소를 사용하여, 이 장치에서 받아야 할 뉴스의 범주를 저장합니다. 이러한 범주를 등록하기 위한 메서드도 이 클래스에 포함됩니다.

4. 위 코드에서 `<hub name>`과 `<connection string with listen access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던  *DefaultListenSharedAccessSignature*의 연결 문자열로 바꿉니다.

	> [AZURE.NOTE] 클라이언트 앱과 함께 배포되는 자격 증명은 일반적으로 안전하지 않기 때문에 클라이언트 앱과 함께 listen access용 키만 배포해야 합니다. Listen access를 통해 앱에서 알림을 등록할 수 있지만, 기존 등록을 수정할 수 없으며 알림을 전송할 수도 없습니다. 안전한 백 엔드 서비스에서 알림을 보내고 기존 등록을 변경하는 데에는 모든 액세스 키가 사용됩니다.

4. **MainActivity** 클래스에서 **NotificationHub** 및 **GoogleCloudMessaging**에 대한 Private 필드를 제거하고 **Notifications**에 대한 필드를 추가합니다.

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;

5. **onCreate** 메서드에서 **hub** 필드 및 **registerWithNotificationHubs** 메서드의 초기화를 제거합니다. 그런 다음 **Notifications** 클래스 인스턴스를 초기화하는 다음 줄을 추가합니다. 메서드에는 다음 줄이 포함되어야 합니다.

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
	
			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);
	
			notifications = new Notifications(this, SENDER_ID);
		}

6. 그 후에 다음 메서드를 추가합니다.
	
	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();
	
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");
	
			notifications.storeCategoriesAndSubscribe(categories);
	    }
	
	이 메서드는 범주 목록을 만들고 **Notifications** 클래스를 사용하여 로컬 저장소에 목록을 저장하고 알림 허브에 해당 태그를 등록합니다. 범주가 변경되면 새 범주로 등록이 다시 생성됩니다.

이제 사용자가 범주 선택을 변경할 때마다 앱은 범주 집합을 장치의 로컬 저장소에 저장하고 알림 허브에 등록할 수 있습니다. 

##<a name="register"></a>알림 등록

다음 단계에서는 로컬 저장소에 저장된 범주를 사용하여 시작 시 알림 허브에 등록합니다. 

> [AZURE.NOTE] GCM(Google Cloud Messaging)에서 할당하는 registrationId는 언제든지 변경될 수 있으므로 알림 실패를 피하려면 알림을 자주 등록해야 합니다. 이 예제에서는 앱이 시작될 때마다 알림을 등록합니다. 자주(하루 두 번 이상) 실행되는 앱에서는 이전 등록 이후 만 하루가 지나지 않은 경우 대역폭 유지를 위한 등록을 건너뛸 수 있습니다.

1. **Notifications** 클래스에 다음 코드를 추가합니다.

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	이렇게 하면 클래스에 정의된 범주가 반환됩니다.

2. 이제 **MainActivity** 클래스에서 **onCreate** 메서드의 끝에 이 코드를 추가합니다.

		notifications.subscribeToCategories(notifications.retrieveCategories());

	이제 앱이 시작될 때마다 로컬 저장소에서 범주를 검색하고, 이러한 범주에 대한 등록을 요청하게 됩니다. [알림 허브 시작] 자습서에서 **InitNotificationsAsync** 메서드를 만들었지만 이 항목에서는 필요하지 않습니다.

3. 그리고 나서 **MainActivity**에 다음 메서드를 추가합니다.

		@Override
		protected void onStart() {
			super.onStart();
			
			Set<String> categories = notifications.retrieveCategories();
			
			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	전에 저장한 범주의 상태를 기반으로 기본 작업이 업데이트됩니다. 

이제 앱이 완료되며, 사용자가 범주 선택을 변경할 때마다 알림 허브 등록에 사용된 장치의 로컬 저장소에 범주 집합을 저장할 수 있습니다. 다음에는 범주 알림을 이 앱에 보낼 수 있는 백 엔드를 정의합니다.

<h2><a name="send"></a>백 엔드에서 알림 보내기</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>앱 실행 및 알림 생성

1. Eclipse에서는 앱을 빌드하고 장치나 에뮬레이터에서 시작합니다.
	
	앱 UI는 구독할 범주를 선택하도록 하는 토글 집합을 제공합니다. 

2. 하나 이상의 범주 토글을 사용하도록 설정한 후 **구독**을 클릭합니다.

	앱은 선택한 범주를 태그로 변환하고 알림 허브에서 선택한 태그에 대한 새로운 장치 등록을 요청합니다. 등록된 범주가 반환되어 대화 상자에 표시됩니다.

4. 다음 방법 중 하나로 백 엔드에서 새 알림을 보냅니다.

	+ **.NET 콘솔 앱:** 콘솔 앱을 시작합니다.

	+ **Java/PHP:** 앱/스크립트를 실행합니다.

	선택한 범주에 대한 알림이 알림 메시지로 나타납니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범주별로 속보를 브로드캐스트하는 방법에 대해 알아보았습니다. 이제 기타 고급 알림 허브 시나리오를 다루는 다음 자습서 중 하나를 완료해 보세요.

+ [알림 허브를 사용하여 지역화된 속보 브로드캐스트]

	지역화된 알림을 보낼 수 있도록 속보 앱을 확장하는 방법에 대해 알아보세요. 

+ [알림 허브를 통해 사용자에게 알림]

	인증된 특정 사용자에게 알림을 푸시하는 방법에 대해 알아보세요. 이 방법은 특정 사용자에게만 알림을 보내기 위한 훌륭한 솔루션입니다.


<!-- Anchors. -->
[앱에 범주 선택 추가]: #adding-categories
[알림 등록]: #register
[백 엔드에서 알림 보내기]: #send
[앱 실행 및 알림 생성]: #test-app
[다음 단계]: #next-steps

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[알림 허브를 사용하여 지역화된 속보 브로드캐스트]: /manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[알림 허브를 통해 사용자에게 알림]: /manage/services/notification-hubs/notify-users
[모바일 서비스]: /develop/mobile/tutorials/get-started/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Windows 스토어용 알림 허브 방법]: http://msdn.microsoft.com/library/jj927172.aspx
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure 관리 포털]: https://manage.windowsazure.com/
[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!--HONumber=49--> 