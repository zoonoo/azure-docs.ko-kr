<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Azure 웹 사이트에 대한 GoDaddy 도메인 이름 구성" metaKeywords="Azure, Azure 웹 사이트, 도메인 이름" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Go Daddy](https://godaddy.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에서 도메인 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 GoDaddy에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. GoDaddy.comy용 DNS 도구를 찾으려면 다음 단계를 따르세요.

1. 사용 중인 계정으로 GoDaddy.com에 로그인하고 **My Account**를 선택한 후**Manage my domains**를 선택합니다. 마지막으로 Azure 웹 사이트에서 사용할 도메인 이름에 대한 드롭다운 메뉴를 선택한 후 **Manage DNS**를 선택합니다.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. **Domain details** 페이지에서 **DNS Zone File** 탭으로 스크롤합니다. 이는 도메인 이름의 DNS 레코드를 추가 및 수정할 때 사용한 섹션입니다. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	**Add Record**를 선택하여 기존 레코드를 추가합니다.

	기존 레코드에 대해 **edit**를 수행하려면 해당 레코드 옆의 펜과 종이 아이콘을 선택합니다.

	> [WACOM.NOTE] 새 레코드를 추가하기 전에 GoDaddy에서 많이 사용되는 하위 도메인에 대한 DNS 레코드(편집기에서 **Host**)(예: **email**, **files**, **mail** 등)를 이미 만든 것을 볼 수 있습니다. 사용하려는 이름이 이미 있는 경우 새로 만들지 않고 기존 레코드를 수정하세요.

4. 레코드를 추가할 때 먼저 레코드 유형을 선택해야 합니다.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	그런 다음 **Host**(사용자 지정 도메인 또는 하위 도메인) 및 해당 호스트의 **Points to** 항목을 제공해야 합니다.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* **A (host) record**를 추가할 때 **Host** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄), *(모든 하위 도메인을 나타내는 와일드카드) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Points to** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.
	
		> [WACOM.NOTE] A(호스트) 레코드를 사용할 때 다음 구성으로 CNAME 레코드도 추가해야 합니다.
		> 
		> * **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 값을 **Points to**하는 **awverify**의 **Host** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

	* **CNAME (alias) record**를 추가할 때 **Host** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Points to** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.


5. 레코드 추가 또는 수정을 완료하면 **Finish**를 클릭하여 변경 내용을 저장합니다.

<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

