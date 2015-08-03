다음 단계에서는 Dropbox.com 사이트를 사용하여 Dropbox 앱을 만드는 프로세스를 보여 줍니다. Dropbox.com 사이트는 예고 없이 변경될 수 있으므로 UI가 표시된 것과 다를 수도 있습니다.

1. [Dropbox 개발자 포털](https://www.dropbox.com/developers/apps)로 이동하여 **앱 콘솔**을 클릭한 다음 **앱 만들기**를 클릭합니다.

	![Dropbox 앱 만들기](./media/app-service-api-create-dropbox-app/dbappcreate.png)

2. **Dropbox API 앱**을 선택하고 기타 설정을 구성합니다.
 
	아래 스크린샷에 표시된 파일 액세스 옵션을 사용하면 계정에 파일이 있을 경우 간단한 HTTP Get 요청으로 Dropbox 계정에 대한 액세스를 테스트할 수 있습니다.

	Dropbox 사이트에서 수락하기만 하면 아무 이름이나 Dropbox API 앱의 이름으로 사용할 수 있습니다.

3. **앱 만들기**를 클릭합니다.

	![Dropbox 앱 만들기](./media/app-service-api-create-dropbox-app/dbapiapp.png)

	다음 페이지에서는 Azure Dropbox 커넥터를 구성하는 데 사용할 앱 키와 앱 암호 설정(Azure에서는 클라이언트 ID 및 클라이언트 암호라고 함)을 보여 줍니다.

	이 페이지에는 리디렉션 URI를 입력할 수 있는 필드도 있습니다. 해당 값은 다음 섹션에서 얻을 수 있습니다.

	![Dropbox 앱 만들기](./media/app-service-api-create-dropbox-app/dbappsettings.png)

<!---HONumber=July15_HO4-->