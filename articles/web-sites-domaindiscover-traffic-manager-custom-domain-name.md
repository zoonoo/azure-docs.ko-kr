<properties 
	pageTitle="트래픽 관리자를 사용하여 Azure에 대한 DomainDiscover 도메인 이름 구성" 
	description="DomainDiscover - TierraNet에 등록된 도메인 이름을 사용하도록 트래픽 관리자를 사용하는 Azure 웹 사이트를 구성하는 방법에 대해 알아봅니다." 
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

#트래픽 관리자를 사용하여 Microsoft Azure 웹 사이트용으로 사용자 지정 도메인 이름 구성(DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">네트워크 솔루션</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [DomainDiscover.com](https://domaindiscover.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다. DomainDiscover.com은 [TierraNet](https://www.tierra.net/)의 일부입니다.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용:

-   [DNS 레코드 이해](#understanding-records)
-   [표준 모드에 대해 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에 트래픽 관리자 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>표준 모드에 대해 웹 사이트 구성</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 DomainDiscover에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DomainDiscover.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1. **Login** 메뉴에서 **Control Panel**을 선택하여 사용 중인 계정으로 DomainDiscover.com(TierraNet)에 로그온합니다.

    ![DomainDiscover 로그인 메뉴](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. **Domain Services** 페이지에서 Azure 웹 사이트에 사용할 도메인을 선택합니다.

    ![도메인 관리 페이지](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. 도메인 설정에서 **DNS Service**의 **Edit** 단추를 클릭합니다.

    ![DNS 편집 단추](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. **Manage DNS** 창의 **Add Records** 목록에서 추가할 DNS 레코드 유형을 선택합니다. 그런 다음 **추가** 단추를 클릭합니다.

    ![DNS 편집 단추](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. 다음 페이지에서 DNS 레코드 값을 입력합니다. 그런 다음 **추가** 단추를 클릭합니다.

    ![DNS 편집 단추](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    * CNAME 레코드를 추가할 때 **Manage DNS** 페이지에서 먼저 **CNAME (Alias)**을 선택해야 합니다. 그런 다음 **Host** 필드를, 사용할 하위 도메인으로 설정합니다. 예를 들면 **www**로 설정해야 합니다. **Alias Hostname** 필드는 Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다. 그런 다음 1800초와 같은 TTL(Time-to-Live) 값을 입력하세요.

	    > [AZURE.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

<h2><a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
