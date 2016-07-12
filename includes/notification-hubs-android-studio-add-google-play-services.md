1. Android Studio의 도구 모음에서 아이콘을 클릭하거나 메뉴에서 **도구** -> **Android** -> **SDK Manager**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾아서 **패키지 정보 표시**를 클릭하여 열고 **Google API**가 아직 설치되어 있지 않으면 선택합니다.

2. **SDK 도구** 탭을 클릭합니다. Google Play Service를 아직 설치하지 않은 경우 아래와 같이 **Google Play Services**를 클릭합니다. **적용**을 클릭하여 설치합니다.
 
	이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

   	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. 앱 디렉터리에서 **build.gradle** 파일을 엽니다.

	![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. *종속성* 아래에 이 줄 추가:

   		compile 'com.google.android.gms:play-services-gcm:9.2.0'

5. 도구 모음에서 **Gradle 파일과 프로젝트 동기화** 아이콘을 클릭합니다.

6. **AndroidManifest.xml**을 열고 이 태그를 *응용프로그램* 태그에 추가합니다.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_0706_2016-->