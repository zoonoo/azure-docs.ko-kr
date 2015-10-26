도메인 이름의 레코드가 전파된 후 해당 레코드를 웹앱과 연결해야 합니다. 웹 브라우저를 사용하여 도메인 이름을 사용하도록 설정하려면 다음 단계를 따르세요.

> [AZURE.NOTE]이전 단계에서 만든 CNAME 레코드가 DNS 시스템을 통해 전파되는 데 어느 정도 시간이 걸릴 수 있습니다. CNAME이 전파될 때까지 웹앱에 도메인 이름을 추가할 수 없습니다. A 레코드를 사용하는 경우 이전 단계에서 만든 **awverify** CNAME 레코드가 전파될 때까지 웹앱에 A 레코드 도메인 이름을 추가할 수 없습니다.
>
> <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.

1. 브라우저에서 [Azure 관리 포털](https://portal.azure.com)을 엽니다.

2. **웹앱** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인 및 SSL**을 선택합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. **사용자 지정 도메인 및 SSL** 블레이드에서 **외부 도메인 가져오기**를 클릭합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. **도메인 이름** 텍스트 상자에 이 웹앱과 연결할 도메인 이름을 입력합니다.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. **저장**을 클릭하여 도메인 이름 구성을 저장합니다.

	구성이 완료되면 사용자 지정 도메인 이름이 웹앱의 **도메인 이름** 섹션에 표시됩니다.

이때 브라우저에서 사용자 지정 도메인 이름을 입력해야 웹앱으로 이동하는지 확인할 수 있습니다.

<!---HONumber=Oct15_HO3-->