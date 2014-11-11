<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# 트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [Dotster.com][Dotster.com]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Dotster에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Dotster.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 Dotster.com에 로그온합니다. **Domain** 메뉴에서 **DomainCentral**을 선택합니다.

    ![Domain Central Dotster 메뉴][Domain Central Dotster 메뉴]

2.  도메인을 선택하여 설정 목록을 표시합니다. 그런 다음 **Nameservers** 링크를 선택합니다.

    ![Dotster Domain 구성 옵션][Dotster Domain 구성 옵션]

3.  **Use different name servers**를 선택합니다. Dotster의 DNS 서비스를 활용하려면 이름 서버인 ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com 및 ns4.nameresolve.com을 지정해야 합니다.

    ![Dotster Domain 구성 옵션][1]

    > [WACOM.NOTE] 이름 서버 변경을 적용하는 데 24 - 48시간이 걸릴 수 있습니다. 이 문서의 남은 단계는, 이름 서버 변경이 적용된 후에야 수행해야 합니다.

4.  DomainCentral에서 도메인을 선택한 후 **DNS**를 선택합니다. **Modify** 목록에서 추가할 DNS 레코드 유형(**CNAME Alias** 또는 **A Record**)을 선택합니다.

    ![Dotster Domain 구성 옵션][2]

5.  그런 다음 레코드의 **Host** 및 **Points To** 필드를 지정합니다. 이 작업을 완료하면 **Add** 단추를 클릭합니다.

    ![Dotster Domain 구성 옵션][3]

    -   CNAME 레코드를 추가할 때 **Host** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Points To** 필드는 Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다.

        > [WACOM.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

## <a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용

[WACOM.INCLUDE [modes][4]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Dotster.com]: https://dotster.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [표준 모드에 대해 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에 트래픽 관리자 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Domain Central Dotster 메뉴]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Dotster Domain 구성 옵션]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
  [4]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
