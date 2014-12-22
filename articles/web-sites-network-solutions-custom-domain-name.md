<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Azure 웹 사이트에 대한 Network Solutions 도메인 이름 구성" metaKeywords="Azure, Azure 웹 사이트, 도메인 이름" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Network Solutions](https://www.networksolutions.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에서 도메인 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>기본, 공유 또는 표준 모드에 대한 웹 사이트 구성</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Network Solutions에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. networksolutions.com용 DNS 도구를 찾아 사용하려면 다음 단계를 따르세요.

1. networksolutions.com에서 사용 중인 계정으로 로그온하고 오른쪽 상단에서 **My Account**를 선택합니다.

3. **My Products and Services** 탭에서 **Edit DNS**를 선택합니다.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. **Domain Names** 페이지의 **Manage <yourdomainname>** 섹션에서 **Edit Advanced DNS Records**를 선택합니다.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. **Update Advanced DNS** 페이지에는 각 레코드 유형의 섹션과 각 섹션 아래에 **Edit** 단추가 포함되어 있습니다.
	
	* A 레코드에 대해 **IP Address (A Records)** 섹션을 사용합니다.
	* CNAME 레코드에 대해 **Host Alias (CNAME Records)** 섹션을 사용합니다.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. **Edit** 단추를 클릭하면 기존 레코드를 수정하거나 새로운 레코드를 추가할 수 있는 양식이 표시됩니다. 

	> [WACOM.NOTE] 새로운 항목을 추가하기 전에 Network Solutions에서 하위 도메인의 루트 도메인('@') 및 와일드카드 레코드('*')와 같은 몇몇 기본 DNS 레코드를 이미 만든 것을 볼 수 있습니다. 사용하려는 레코드가 이미 있는 경우 새로 만들지 않고 기존 레코드를 수정하세요.

	* CNAME 레코드를 추가할 때 **Alias** 필드는 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Other host** 필드 옆의 원형 필드를 선택하고 **Other host**를 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다. 이 필드는 Azure 웹 사이트에 사용할 CNAME 레코드를 만들 때 필요하지 않으므로 **Refers to Host Name**을 **Select**로 두세요.
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
		> 
		> * **www**의 **Alias** 값 및 **&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 또는
		> 
		> * **awverify.www**의 **Alias** 값 및 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

	* A 레코드를 추가할 때 **Host** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄), *(모든 하위 도메인을 나타내는 와일드카드) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Numeric IP** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. 레코드 추가 또는 수정을 완료하면 **Continue**를 클릭하여 변경 내용을 검토합니다. **Save changes only**를 선택하여 변경 내용을 저장합니다.

<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
