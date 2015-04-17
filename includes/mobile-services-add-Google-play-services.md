1. Android Studio의 도구 모음 오른쪽 끝에서 아이콘을 클릭하여 Android SDK Manager를 엽니다. 해당 아이콘의 모양을 모르는 경우에는 마우스 포인터로 아이콘을 가리킵니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾아 열고 **Google API**가 아직 설치되어 있지 않으면 선택합니다.

2. 아래쪽으로 스크롤하여 **추가**가 표시되면 확장한 다음 아래와 같이 **Google Play Services**를 선택합니다. **패키지 설치**를 클릭합니다. 다음 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다. 

   ![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 앱 라이브러리에서 **build.gradle** 파일을 엽니다.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. 다음 줄을 *dependencies* 아래에 추가합니다. 

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. 그런 다음 *defaultConfig* 아래에서 *minSdkVersion*을 9로 변경합니다.
 
6. 도구 모음에서 **Gradle 파일과 프로젝트 동기화** 단추를 클릭합니다.

7. **AndroidManifest.xml**을 열고 다음 태그를 *application* 태그에 추가합니다.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 




<!--HONumber=49-->