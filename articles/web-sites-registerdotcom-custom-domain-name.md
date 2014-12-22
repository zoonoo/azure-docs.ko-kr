<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Azure 웹 사이트에 대한 Register.com 도메인 이름 구성" metaKeywords="Azure, Azure 웹 사이트, 도메인 이름" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Register.com](https://www.register.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인을 Azure 웹 사이트에 연결하려면 Register.com에서 제공하는 도구를 사용하여 DNS 테이블에 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 따르세요.

1. register.com에서 사용 중인 계정에 로그온하고 오른쪽 위 모서리에서 **Your Account**를 클릭하여 도메인을 본 후 사용자 지정 도메인 이름을 선택합니다.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. **고급 기술 설정**이 보일 때까지 페이지 아래로 이동합니다. 이 섹션의 링크에서 도메인에 대한 레코드를 관리할 수 있습니다.

	* A 레코드의 경우 **IP 주소 레코드 편집** 링크를 사용합니다.
	* CNAME 레코드의 경우 **도메인 별칭 레코드 편집** 링크를 사용합니다.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. **Edit** 단추를 클릭하면 기존 레코드를 수정하거나 새로운 레코드를 추가할 수 있는 양식이 표시됩니다. CNAME 및 A 레코드의 형식은 서로 유사합니다.

	* CNAME 레코드를 추가할 때 **.mydomainname.com** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름을 가리키는 **points to** 값을 선택해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다. 이 필드는 Azure 웹 사이트에 사용할 CNAME 레코드를 만들 때 필요하지 않으므로 **Refers to Host Name**을 **Select**로 두세요.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
		> 
		> * **www**의 **Alias** 값 및 **&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 또는
		> 
		> * **awverify.www**의 **Alias** 값 및 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

	* A 레코드를 추가할 때 **.mydomainname.com** 필드를, 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. 루트 도메인을 설정하려면 필드를 공백으로 두고, 와일드카드 매핑을 만들려면 **\**를 사용합니다. **Points to** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. 레코드 추가 또는 수정을 완료하면 **Continue**를 클릭하여 변경 내용을 검토합니다. **Continue**를 다시 선택하여 변경 내용이 저장됩니다.

<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
