

1. Eclipse 위쪽 도구 모음에서 **Window**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트 속성에서 지정한 Android SDK의 대상 버전을 찾아 열고 **Google APIs**를 선택합니다.

2. **Extras**까지 아래로 스크롤하여 확장하고 아래와 같이 **Google Play Services**를 선택합니다. **Install Packages**를 클릭합니다. 다음 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다. Eclipse를 다시 시작합니다.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 프로젝트에서 Google Play Services SDK를 설치합니다. Eclipse에서 **File**, **Import**를 차례로 클릭합니다. **Android**, **Existing Android Code into Workspace**를 차례로 선택하고 **Next**를 클릭합니다. **찾아보기**를 클릭하고 Android SDK 경로(일반적으로 Eclipse를 포함하는 폴더 안의 이름이 `adt-bundle-windows-x86_64`인 폴더에서)로 이동한 다음 `\extras\google\google_play_services\libproject` 하위 폴더로 이동하여 google-play-services-lib 폴더를 선택하고 **확인**을 클릭합니다. **Copy projects into workspace** 확인란을 선택한 후 **Finish**를 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. 그런 다음, 프로젝트에서 방금 가져온 Google Play Services SDK 라이브러리를 참조해야 합니다.

5. **Package Explorer**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 *Properties*를 선택합니다.
 
6. Properties 창에서 왼쪽의 Android를 선택합니다.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. **프로젝트 빌드 대상**에서 적절한 SDK 수준에 대해 `Google APIs x86`(또는 개발 플랫폼에 따라 `Google APIs`)을(를) 선택해야 합니다.

 
8. **Library** 섹션에서 **Add**를 선택하여 Google Play Services 프로젝트(*google-play-services-lib*)를 선택하고 **확인**을 클릭합니다.

9. **Apply**를 클릭한 후 **OK**를 클릭합니다.

<!---HONumber=July15_HO3-->