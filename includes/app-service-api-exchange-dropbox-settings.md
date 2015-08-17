4. 다른 브라우저 창이나 탭에서 [Azure Preview 포털](https://portal.azure.com)로 이동합니다.

3. Dropbox 커넥터에 대한 **API 앱** 블레이드로 이동합니다. 여전히 **리소스 그룹** 블레이드에 있는 경우 다이어그램에서 Dropbox 커넥터를 클릭하면 됩니다.

4. **설정**을 클릭한 다음 **설정** 블레이드에서 **인증**을 클릭합니다.

	![설정 클릭](./media/app-service-api-exchange-dropbox-settings/clicksettings.png)

	![인증 클릭](./media/app-service-api-exchange-dropbox-settings/clickauth.png)

5. 인증 블레이드에서 Dropbox 사이트의 클라이언트 ID 및 클라이언트 암호를 입력하고 **저장**을 클릭합니다.

	![설정을 입력하고 저장 클릭](./media/app-service-api-exchange-dropbox-settings/authblade.png)

3. **리디렉션 URI**(클라이언트 ID 및 클라이언트 암호 위에 있는 회색 상자)를 복사하여 이전 단계에서 열어 둔 페이지에 값을 추가합니다.

	리디렉션 URI는 다음 패턴을 따릅니다.

		[gatewayurl]/api/consent/redirect/[connectorname]

	예:

		https://dropboxrgaeb4ae60b7.azurewebsites.net/api/consent/redirect/DropboxConnector

	![리디렉션 URI 가져오기](./media/app-service-api-exchange-dropbox-settings/redirecturi.png)

	![Dropbox 앱 만들기](./media/app-service-api-exchange-dropbox-settings/dbappsettings2.png)

<!---HONumber=August15_HO6-->