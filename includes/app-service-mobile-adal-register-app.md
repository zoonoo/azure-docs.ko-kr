1. [Azure Active Directory로 모바일 앱을 구성하는 방법] 항목에 따라 Azure Active Directory 테넌트로 모바일 앱 백엔드를 등록합니다.
2. [Azure 클래식 포털]에서 **Active Directory**로 이동합니다.
   
   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)
3. 디렉터리를 선택한 다음 맨 위에서 **응용 프로그램** 탭을 선택합니다. 맨 아래에서 **추가**를 클릭하여 새로운 앱 등록을 만듭니다. 
4. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.
5. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램** 유형을 클릭합니다. 계속하려면 클릭합니다.
6. **리디렉션 URI** 상자에 앱 서비스 게이트웨이로 /login/done 끝점을 입력합니다. 이 값은 https://contoso.azurewebsites.net/login/done과 유사합니다.
7. 네이티브 응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. **클라이언트 ID**를 복사합니다. 이 ID는 나중에 필요합니다.
8. 페이지 아래의 **다른 응용 프로그램에 대한 권한** 섹션으로 스크롤하여 **응용 프로그램 추가**를 클릭합니다.
9. 이전에 등록한 웹 응용 프로그램을 검색하고 더하기 아이콘을 클릭합니다. 그런 다음 체크 표시를 클릭하여 대화 상자를 닫습니다.
10. 방금 추가된 새 항목에서 **권한 위임** 드롭다운을 열고 **액세스(appName)**를 선택합니다. 그런 다음 **저장**을 클릭합니다.
    
    ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

응용 프로그램은 이제 AAD에서 AAD 로그인을 사용하여 사용자가 로그온할 수 있도록 구성되었습니다.

[Azure 클래식 포털]: https://manage.windowsazure.com/
[Azure Active Directory로 모바일 앱을 구성하는 방법]: ../articles/app-service-how-to-configure-active-directory-authentication.md

<!---HONumber=AcomDC_1203_2015-->