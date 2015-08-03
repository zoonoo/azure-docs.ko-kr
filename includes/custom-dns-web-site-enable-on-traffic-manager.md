도메인 이름의 레코드가 전파된 후 브라우저를 사용하여 Azure 앱 서비스의 웹앱에 액세스하는 데 사용자 지정 도메인 이름을 사용할 수 있는지 확인할 수 있습니다.

> [AZURE.NOTE]CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.

웹앱을 트래픽 관리자 끝점으로 아직 추가하지 않은 경우 이름 확인이 적용되기 전에 사용자 지정 도메인 이름이 트래픽 관리자로 라우팅할 때 이를 수행해야 합니다. 그런 다음 트래픽 관리자가 웹앱으로 라우팅됩니다. [끝점 추가 또는 삭제](http://msdn.microsoft.com/library/windowsazure/hh744839.aspx)의 내용을 참조하여 트래픽 관리자 프로필에서 웹앱을 끝점으로 추가합니다.

> [AZURE.NOTE]끝점을 추가할 때 웹앱이 목록에 나열되지 않는 경우 웹앱이 **표준** 앱 서비스 계획 모드로 구성되었는지 확인합니다. **표준** 모드로 구성된 웹앱만이 트래픽 관리자에서 작동합니다.

1. 브라우저에서 [Azure 포털](https://portal.azure.com)을 엽니다.

2. **웹앱** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인 및 SSL**을 선택합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. **사용자 지정 도메인 및 SSL** 블레이드에서 **외부 도메인 가져오기**를 클릭합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. **도메인 이름** 텍스트 상자에 이 웹앱과 연결할 트래픽 관리자 도메인 이름(contoso.trafficmanager.net)을 입력합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. **저장**을 클릭하여 도메인 이름 구성을 저장합니다.

	구성이 완료되면 사용자 지정 도메인 이름이 웹앱의 **도메인 이름** 섹션에 표시됩니다.

이때 브라우저에서 트래픽 관리자 도메인 이름(contoso.trafficmanager.net)을 입력해야 웹앱으로 이동하는지 확인할 수 있습니다.

<!---HONumber=July15_HO4-->