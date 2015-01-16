<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="Azure 웹 사이트에 대한 Dotster 도메인 이름 구성" metaKeywords="Azure, Azure 웹 사이트, Dotster" description="Dotster에 등록된 도메인 이름을 사용하도록 Azure 웹 사이트를 구성하는 방법에 대해 알아봅니다." services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Dotster.com](https://dotster.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Dotster에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Dotster.com용 DNS 도구를 찾으려면 다음 단계를 따르세요.

1. 사용 중인 계정으로 Dotster.com에 로그온합니다. **Domain** 메뉴에서 **DomainCentral**을 선택합니다.

    ![Domain Central Dotster Menu](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. 도메인을 선택하여 설정 목록을 표시합니다. 그런 다음 **Nameservers** 링크를 선택합니다.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. **Use different name servers**를 선택합니다. Dotster의 DNS 서비스를 활용하려면 이름 서버인 ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com, and ns4.nameresolve.com을 지정해야 합니다.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] 이름 서버 변경을 적용하는 데 24 - 48시간이 걸릴 수 있습니다. 이 문서의 남은 단계는, 이름 서버 변경이 적용된 후에야 수행해야 합니다.

4. DomainCentral에서 도메인을 선택한 후 **DNS**를 선택합니다. **Modify** 목록에서 추가할 DNS 레코드 유형(**CNAME Alias** 또는 **A Record**)을 선택합니다. 

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. 그런 다음 레코드의 **Host** 및 **Points To** 필드를 지정합니다. 이 작업을 완료하면 **Add** 단추를 클릭합니다.

    ![Dotster Domain Configuration Options](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * CNAME 레코드를 추가할 때 **Host** 필드는 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Points To** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

    * A 레코드를 추가할 때 **Host** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Points To** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

		> [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
		> 
		> * **&lt;yourwebsitename&gt;.azurewebsites.net**의 값을 **가리키는** **www**의 **Host** 값
		> 
		> 또는
		> 
		> * **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**의 값을 **가리키는** **awverify.www**의 **Host** 값
		> 
		> 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.


<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
