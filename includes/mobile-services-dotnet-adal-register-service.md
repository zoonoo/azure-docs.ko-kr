## <a name="register-mobile-service-aad"></a>Azure Active Directory에 모바일 서비스 등록


이 섹션에서는 Azure Active Directory에 모바일 서비스를 등록하고 Single Sign-On 가장을 허용하도록 사용 권한을 구성합니다.

1. [Azure Active Directory에 등록하는 방법] 항목에 따라 Azure Active Directory에 응용 프로그램을 등록합니다.

2. [Azure 관리 포털]에서, Azure Active Directory 확장으로 다시 이동하고 활성 디렉터리를 클릭합니다.

3. **응용 프로그램** 탭을 클릭한 다음, 응용 프로그램을 클릭합니다.

4. **매니페스트 관리**를 클릭합니다. 그런 다음 **매니페스트 다운로드**를 클릭하고 로컬 디렉터리에 응용 프로그램 매니페스트를 저장합니다.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Visual Studio에서 응용 프로그램 매니페스트 파일을 엽니다. 파일의 위쪽에서 다음과 같은 앱 사용 권한 줄을 찾습니다.

        "oauth2Permissions": [],

    이 줄을 다음 앱 사용 권한으로 바꾸고 파일을 저장합니다.

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. Azure 관리 포털에서 응용 프로그램에 대한 **매니페스트 관리**를 다시 클릭하고 **매니페스트 업로드**를 클릭합니다.  방금 업로드한 응용 프로그램 매니페스트의 위치로 이동하여 매니페스트를 업로드합니다.

<!-- URLs. -->
[Azure Active Directory에 등록하는 방법]: /ko-kr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure 관리 포털]: https://manage.windowsazure.com/


<!--HONumber=42-->
