## <a name="register-app-aad"></a>Azure Active Directory에 클라이언트 앱 등록

1.  [관리 포털][]에서 **Active Directory**로 이동한 다음 디렉터리를 클릭합니다.

![][]

1.  맨 위에 있는 **응용 프로그램** 탭을 클릭한 다음 앱을 클릭하여 **추가**합니다.

![][1]

1.  **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

2.  응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램** 유형을 클릭합니다. 계속하려면 클릭합니다.

![][2]

1.  **리디렉션 URI** 상자에 모바일 서비스의 /login/done 끝점을 입력합니다. 이 값은 <https://todolist.azure-mobile.net/login/done>와 유사합니다.

![][3]

1.  네이티브 응용 프로그램의 **구성** 탭을 클릭하고 **클라이언트 ID**를 복사합니다. 이 ID는 나중에 필요합니다.

![][4]

1.  페이지 아래의 **다른 응용 프로그램에 대한 권한** 섹션으로 스크롤하여 앞서 등록한 모바일 서비스 응용 프로그램에 대한 모든 권한을 부여합니다. 그런 다음 **저장**을 클릭합니다.

![][5]

이제 모바일 서비스는 AAD에서 앱의 Single Sign-On을 받도록 구성됩니다.

  [관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
