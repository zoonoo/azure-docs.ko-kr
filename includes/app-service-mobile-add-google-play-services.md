1. Android Studio의 도구 모음에서 아이콘을 클릭하거나 메뉴에서 **도구** -> **Android** -> **SDK Manager**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾아 열고 **Google API**가 아직 설치되어 있지 않으면 선택합니다.

2. **파일**, **프로젝트 구조**로 이동합니다. 그런 다음 **알림**에서 푸시 알림을 활성화합니다.

3. **AndroidManifest.xml**을 열고 이 태그를 *응용프로그램* 태그에 추가합니다.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->