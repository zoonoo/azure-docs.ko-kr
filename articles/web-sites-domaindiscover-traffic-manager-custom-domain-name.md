<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# 트래픽 관리자를 사용하여 Azure 웹 사이트용으로 사용자 지정 도메인 이름 구성(DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [DomainDiscover.com][DomainDiscover.com]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다. DomainDiscover.com은 [TierraNet][TierraNet]의 일부입니다.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해][DNS 레코드 이해]
-   [표준 모드에 대해 웹 사이트 구성][표준 모드에 대해 웹 사이트 구성]
-   [사용자 지정 도메인에 대한 DNS 레코드 추가][사용자 지정 도메인에 대한 DNS 레코드 추가]
-   [웹 사이트에 트래픽 관리자 사용][웹 사이트에 트래픽 관리자 사용]

## <a name="understanding-records"></a>DNS 레코드 이해

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>표준 모드에 대해 웹 사이트 구성

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## 사용자 지정 도메인에 대한 DNS 레코드 추가

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 DomainDiscover에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DomainDiscover.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  **Login** 메뉴에서 **Control Panel**을 선택하여 사용 중인 계정으로 DomainDiscover.com(TierraNet)에 로그온합니다.

    ![DomainDiscover 로그인 메뉴][DomainDiscover 로그인 메뉴]

2.  **Domain Services** 페이지에서 Azure 웹 사이트에 사용할 도메인을 선택합니다.

    ![도메인 관리 페이지][도메인 관리 페이지]

3.  도메인 설정에서 **DNS Service**의 **Edit** 단추를 클릭합니다.

    ![DNS 편집 단추][DNS 편집 단추]

4.  **Manage DNS** 창의 **Add Records** 목록에서 추가할 DNS 레코드 유형을 선택합니다. 그런 다음 **추가** 단추를 클릭합니다.

    ![DNS 편집 단추][1]

5.  다음 페이지에서 DNS 레코드 값을 입력합니다. 그런 다음 **추가** 단추를 클릭합니다.

    ![DNS 편집 단추][2]

    -   CNAME 레코드를 추가할 때 **Manage DNS** 페이지에서 먼저 **CNAME (Alias)**을 선택해야 합니다. 그런 다음 **Host** 필드를, 사용할 하위 도메인으로 설정합니다. 예를 들면 **www**로 설정해야 합니다. **Alias Hostname** 필드는 Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다. 그런 다음 1800초와 같은 TTL(Time-to-Live) 값을 입력하십시오.

        > [WACOM.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

## <a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용

[WACOM.INCLUDE [modes][3]]

  [사용자 지정 도메인]: /ko-KR/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인"
  [GoDaddy]: /ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ko-KR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ko-KR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ko-KR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ko-KR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [웹 사이트]: /ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "웹 사이트"
  [트래픽 관리자를 사용하는 웹 사이트]: /ko-KR/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [DomainDiscover.com]: https://domaindiscover.com
  [TierraNet]: https://www.tierra.net/
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [표준 모드에 대해 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에 트래픽 관리자 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [DomainDiscover 로그인 메뉴]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [도메인 관리 페이지]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [DNS 편집 단추]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png
  [3]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
