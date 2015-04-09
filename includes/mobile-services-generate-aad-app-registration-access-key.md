1. [Azure 관리 포털](https://manage.windowsazure.com/)의 디렉터리 페이지에서 **응용 프로그램** 탭을 클릭합니다.

2. 통합된 응용 프로그램 등록을 클릭합니다.

3. 응용 프로그램 페이지에서 **구성**을 클릭하고 페이지의 **키** 섹션까지 아래로 스크롤합니다. 
4. 새 키에 대해 **1년** 기간을 클릭합니다. 그런 다음 **저장**을 클릭하면 포털에서 새 키 값을 표시합니다.
5. 저장한 후 표시된 **클라이언트 ID** 및 **키**를 복사합니다. 키 값은 저장한 후 1번만 표시됩니다. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. 통합 응용 프로그램 구성 페이지의 아래쪽으로 스크롤하여 응용 프로그램에 대한 **디렉터리 데이터 읽기** 권한을 사용하도록 설정하고 **저장**을 클릭합니다.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. [Azure 관리 포털](https://manage.windowsazure.com/)에서 모바일 서비스로 다시 이동한 다음 **구성** 탭을 클릭합니다. **앱 설정** 섹션까지 아래로 스크롤하여 다음 앱 설정을 추가하고 **저장**을 클릭합니다. 

    <table border="1">
    <tr>
    <th>앱 설정 이름</th><th>설명</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>위의 단계에 있는 통합 앱에서 복사한 클라이언트 ID입니다.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>위의 단계에서 AAD 통합 앱에서 생성한 앱 키입니다.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>AAD 도메인 이름입니다. "mydomain.onmicrosoft.com"과 유사합니다.</td>
    </tr>
    </table><br/>


    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)


<!--HONumber=49-->