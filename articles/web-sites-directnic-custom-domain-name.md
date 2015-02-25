<properties 
	pageTitle="Azure 웹 사이트에 대한 Directnic 도메인 이름 구성" 
	description="Directnic에 등록된 도메인 이름을 사용하도록 Azure 웹 사이트를 구성하는 방법에 대해 알아봅니다." 
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
	ms.author="larryfr,jroth"/>

#Azure 웹 사이트에 대한 Directnic 도메인 이름 구성(Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">네트워크 솔루션</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [DirectNic.com](https://directnic.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용:

-   [DNS 레코드 이해](#understanding-records)
-   [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에서 도메인 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>기본, 공유 또는 표준 모드에 대한 웹 사이트 구성</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Directnic에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Directnic.com용 DNS 도구를 찾으려면 다음 단계를 따르세요.

1. 사용 중인 계정으로 Directnic.com에 로그인하고 **My Services**를 선택한 후 **Domains**를 선택합니다.

    ![Directnic 서비스 메뉴](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Azure 웹 사이트에서 사용할 도메인 이름을 클릭합니다.

2. 도메인 관리 페이지의 **Services** 창에서 **DNS**의 **Manage** 단추를 클릭합니다.

    ![서비스 패널](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. **Type**, **Name** 및 **Data** 필드를 입력하여 DNS 레코드를 추가합니다. 이 작업을 완료하면 **Add Record** 단추를 클릭합니다.   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * CNAME 레코드를 추가할 때 **Name** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Data** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

    * A 레코드를 추가할 때 **Name** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Data** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

		> [AZURE.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
		> 
		> * **www**의 **Name** 값 및 **&lt;yourwebsitename&gt;.azurewebsites.net**의 **Data** 값
		> 
		> 또는
		> 
		> * **awverify.www**의 **Name** 값 및 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 **Data** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.


<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!--HONumber=42-->
