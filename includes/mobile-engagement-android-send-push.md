
###매니페스트 파일을 업데이트하여 알림 사용

아래의 앱 내 메시징 리소스를 Manifest.xml의 `<application>` 태그와 `</application>` 태그 사이에 복사합니다.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

###알림 아이콘 지정

다음 Manifest.xml의 XML 코드 조각을 `<application>` 태그와 `</application>` 태그 사이에 붙여넣습니다.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

시스템 및 앱 내 알림 모두에서 표시되는 아이콘을 정의합니다. 이 아이콘은 앱 내 알림에는 옵션이지만 시스템 알림에는 필수입니다. Android에서는 아이콘이 잘못된 시스템 알림을 거부합니다.

**drawable** 폴더(예: ``engagement_close.png``) 중 하나에 있는 아이콘을 사용하고 있는지 확인하세요. **mipmap** 폴더는 지원되지 않습니다.

>[AZURE.NOTE] **시작 관리자** 아이콘은 사용하면 안 됩니다. 시작 관리자는 해상도가 다르며 일반적으로 지원되지 않는 mipmap 폴더에 있습니다.

실제 앱의 경우 [Android 디자인 지침](http://developer.android.com/design/patterns/notifications.html)에 따라 알림에 적합한 아이콘을 사용할 수 있습니다.

>[AZURE.TIP] 올바른 아이콘 해상도를 사용하는지 확인하려면 [해당 예제](https://www.google.com/design/icons)를 확인하세요. **알림** 섹션이 나올 때까지 아래로 스크롤하고 아이콘을 클릭한 다음 `PNGS`를 클릭하여 아이콘 drawable 집합을 다운로드합니다. 각 아이콘 버전에 사용할 drawable 폴더와 해상도를 확인할 수 있습니다.

###앱이 GCM 푸시 알림을 받을 수 있도록 설정

1. Google Play 콘솔에서 가져온 `project number`을 교체한 후에 다음을 `<application>` 및 `</application>` 사이의 Manifest.xml에 붙여넣습니다. \\n은 프로젝트 번호가 \\n으로 끝나도록 의도적으로 사용되었습니다.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 아래 코드를 Manifest.xml 파일의 `<application>` 태그와 `</application>` 태그 사이에 붙여넣습니다. 패키지 이름 <Your package name>을 바꿉니다.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. `<application>` 태그 앞에 강조 표시된 마지막 권한 집합을 추가합니다. `<Your package name>`을 응용 프로그램의 실제 패키지 이름으로 바꿉니다.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

<!---HONumber=AcomDC_0330_2016-->