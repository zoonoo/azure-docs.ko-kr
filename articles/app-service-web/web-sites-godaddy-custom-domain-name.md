<properties
	pageTitle="Azure 앱 서비스에 대한 사용자 지정 도메인 이름 구성(GoDaddy)"
	description="Azure 웹 앱에서 GoDaddy의 도메인 이름을 사용하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="cephalin"/>

# GoDaddy에서 직접 구입한 Azure 앱 서비스에서 사용자 지정 도메인 이름 구성

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Azure 앱 서비스 웹앱을 통해 도메인을 구입한 경우 [웹앱 도메인 구입](custom-dns-web-site-buydomains-web-app.md)의 최종 단계를 참조하세요.

이 문서에서는 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714)을 사용하여 [GoDaddy](https://godaddy.com)에서 직접 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##DNS 레코드 이해

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## 사용자 지정 도메인에 대한 DNS 레코드 추가

사용자 지정 도메인과 앱 서비스의 웹 앱을 연결하려면 GoDaddy에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. GoDaddy.comy용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1. 사용 중인 계정으로 GoDaddy.com에 로그인하고 **My Account**를 클릭한 후 **Manage my domains**를 클릭합니다. 마지막으로 Azure 웹 앱에서 사용할 도메인 이름에 대한 드롭다운 메뉴를 선택한 후 **Manage DNS**를 선택합니다.

	![GoDaddy의 사용자 지정 도메인 페이지](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. **Domain details** 페이지에서 **DNS Zone File** 탭으로 스크롤합니다. 이는 도메인 이름의 DNS 레코드를 추가 및 수정할 때 사용한 섹션입니다.

	![DNS Zone File 탭](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	**Add Record**를 선택하여 기존 레코드를 추가합니다.

	기존 레코드에 대해 **edit**를 수행하려면 해당 레코드 옆의 펜과 종이 아이콘을 선택합니다.

	> [AZURE.NOTE] 새 레코드를 추가하기 전에 GoDaddy에서 많이 사용되는 하위 도메인에 대한 DNS 레코드(편집기에서 **Host**)(예: **email**, **files**, **mail** 등)를 이미 만든 것을 볼 수 있습니다. 사용하려는 이름이 이미 있는 경우 새로 만들지 않고 기존 레코드를 수정하십시오.

4. 레코드를 추가할 때 먼저 레코드 유형을 선택해야 합니다.

	![레코드 유형 선택](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	그런 다음 **Host**(사용자 지정 도메인 또는 하위 도메인) 및 해당 호스트의 **Points to** 항목을 제공해야 합니다.

	![영역 레코드 추가](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* **A (host) record**를 추가할 때 **Host** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄) * *(모든 하위 도메인을 나타내는 와일드카드) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Points to** 필드를 Azure 웹앱의 IP 주소로 설정해야 합니다.

	* **CNAME (alias) record**를 추가할 때 **Host** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Points to** 필드는 Azure 웹 앱의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurewebsites.net**으로 설정해야 합니다.

5. **다른 항목 추가**를 클릭합니다.
6. 레코드 유형으로 **TXT**를 선택한 다음 **@**의 **호스트** 값 및 **&lt;yourwebappname&gt;.azurewebsites.net**의 **Points to** 값을 지정합니다.

	> [AZURE.NOTE] 이 TXT 레코드는 Azure에서 A 레코드 또는 첫 번째 TXT 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다. 도메인이 Azure 포털에서 웹앱으로 매핑되면 이 TXT 레코드 항목을 제거할 수 있습니다.

5. 레코드 추가 또는 수정을 완료하면 **Finish**를 클릭하여 변경 내용을 저장합니다.

<a name="enabledomain"></a>
## 웹 앱에서 도메인 이름 사용

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!---HONumber=AcomDC_0824_2016-->
