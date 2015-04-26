<properties 
	pageTitle="트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 GoDaddy 도메인 이름 구성" 
	description="Azure 웹 사이트에서 GoDaddy의 도메인 이름을 사용하는 방법에 대해 알아봅니다." 
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

#트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">네트워크 솔루션</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [Go Daddy](https://godaddy.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [표준 모드에 대해 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에 트래픽 관리자 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>표준 모드에 대해 웹 사이트 구성</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 GoDaddy에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. GoDaddy.comy용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1. 사용 중인 계정으로 GoDaddy.com에 로그인하고 **My Account**를 선택한 후 **Manage your domains**를 선택합니다. 마지막으로 Azure 웹 사이트에서 사용할 도메인 이름을 선택합니다.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. **Domain details** 페이지에서 **DNS Zone File** 탭을 선택합니다. 이는 도메인 이름의 DNS 레코드를 추가 및 수정할 때 사용한 섹션입니다. **Edit** 단추를 선택하여 **Zone File Editor**를 표시합니다.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. **Zone File Editor**는 A 레코드(첫 번째 섹션인 **A (Host)**로 나열됨)로 시작하여 CNAME 레코드(**CNAME (Alias)**로 나열됨)가 이어지는 방식으로 각 레코드 유형에 대한 섹션으로 나뉩니다. 새 항목을 추가하려면 해당 섹션 아래의 **Quick Add** 단추를 사용하고, 기존 항목을 편집하려면 항목을 선택하고 기존 정보를 수정하세요.

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [AZURE.NOTE] 영역 파일에 항목을 추가하기 전에 GoDaddy에서 많이 사용되는 하위 도메인에 대한 DNS 레코드(편집기에서 **Host**)(예: **email**, **files**, **mail** 등)를 이미 만든 것을 볼 수 있습니다. 사용하려는 이름이 이미 있는 경우 새로 만들지 않고 기존 레코드를 수정하세요.

	CNAME 레코드를 추가할 때 **host** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Points to** 필드는 Azure 웹 사이트에 사용한 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다.

	> [AZURE.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

5. 레코드 추가 또는 수정을 완료하면 **Save Zone File**을 클릭하여 변경 내용을 저장합니다.

<h2><a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
