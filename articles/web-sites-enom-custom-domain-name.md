<properties 
	pageTitle="Azure 웹 사이트에 대한 eNom 도메인 이름 구성" 
	description="Azure 웹 사이트에서 eNom의 도메인 이름을 사용하는 방법에 대해 알아봅니다." 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth"/>

#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">네트워크 솔루션</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [eNom](https://enom.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에서 도메인 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>기본, 공유 또는 표준 모드에 대한 웹 사이트 구성</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 eNom에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. enom.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1. 사용 중인 계정으로 eNom에 로그온하고 **Domains**를 선택한 후 **My Domains**를 선택합니다. 그러면 도메인 이름이 표시됩니다.

2. **My Domains** 페이지에서 **Manage Domain** 필드를 사용하여 **Host Records**를 선택합니다. 그러면 호스트 레코드 필드가 표시됩니다.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. Host Records 편집기에서 **Record Type** 필드를 사용하여 특정 레코드 유형을 선택할 수 있습니다. Azure 웹 사이트에서 **CNAME (Alias)** 또는 **A (Address)** 선택 항목만 사용해야 합니다.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [AZURE.NOTE]  영역 파일에 항목을 추가하기 전에 eNom에서 루트 도메인('@') 및 하위 도메인(와일드카드)에 대한 DNS 레코드를 이미 만든 것을 볼 수 있습니다. 웹 사이트로 루트 도메인을 리디렉션하거나 와일드카드 A 레코드를 사용하려는 경우 새로 만드는 대신 이러한 항목을 수정해야 합니다.

	* CNAME 레코드를 추가할 때 **Host Name** 필드는 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

		> [AZURE.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
		> 
		> * **www**의 **Alias** 값 및 **&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 또는
		> 
		> * **awverify.www**의 **Alias** 값 및 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 **Other host** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

	* A 레코드를 추가할 때 **Host Name** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄), *(모든 하위 도메인을 나타내는 와일드카드) 또는 사용할 특정 하위 도메인(예: **www**)으로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

		> [AZURE.NOTE] A 레코드를 추가할 때 **awverify**의 호스트 및 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net의 **Points to**를 사용하여 CNAME 레코드도 추가해야 합니다.

5. 레코드 추가 또는 수정을 완료하면 **Save**를 클릭하여 변경 내용을 저장합니다.

<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->
