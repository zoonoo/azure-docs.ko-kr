1. Android Studio의 도구 모음에서 아이콘을 클릭하거나 메뉴에서 **도구** -> **Android** -> **SDK Manager**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾아 열고 **Google API**가 아직 설치되어 있지 않으면 선택합니다.

2. **Extras**까지 아래로 스크롤하여 확장하고 아래와 같이 **Google Play Services**를 선택합니다. **Install Packages**를 클릭합니다. 다음 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 앱 디렉터리에서 **build.gradle** 파일을 엽니다.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. *종속성* 아래에 이 줄 추가:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. *defaultConfig* 아래에서 *minSdkVersion*을 9로 변경합니다.
 
6. 도구 모음에서 **Gradle 파일과 프로젝트 동기화** 아이콘을 클릭합니다.

7. **AndroidManifest.xml**을 열고 이 태그를 *응용프로그램* 태그에 추가합니다.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=Oct15_HO3-->