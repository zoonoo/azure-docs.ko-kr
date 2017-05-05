Azure Portal이 있는 브라우저 창으로 전환합니다.

#### <a name="add-hostname"></a>호스트 이름 추가

**호스트 이름 추가** 옆에 있는 **+** 아이콘을 클릭합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>호스트 이름 유효성 검사

이전에 구성한 CNAME 레코드에 사용할 정규화된 도메인 이름(예: `www.contoso.com`)을 입력한 다음 **유효성 검사**를 클릭합니다.

**호스트 이름 레코드 유형** 아래에서 **CNAME(www.example.com 또는 하위 도메인)** 옵션을 선택합니다.

**호스트 이름 추가**를 클릭하여 앱에 DNS 이름을 추가합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데까지는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.