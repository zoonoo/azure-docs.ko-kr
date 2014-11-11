<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# 트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [Network Solutions][1]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Network Solutions에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 사용하십시오.

1.  networksolutions.com에서 사용 중인 계정으로 로그온하고 오른쪽 위 모서리에서 **My Account**를 선택합니다.

2.  **My Products and Services** 탭에서 **Edit DNS**를 선택합니다.

    ![DNS 편집 페이지][DNS 편집 페이지]

3.  **Domain Names** 페이지의 **Manage <yourdomainname>** 섹션에서 **Edit Advanced DNS Records**를 선택합니다.

    ![Edit Advanced DNS Records가 강조 표시되어 있는 도메인 이름 페이지][Edit Advanced DNS Records가 강조 표시되어 있는 도메인 이름 페이지]

4.  **Update Advanced DNS** 페이지에는 각 레코드 유형의 섹션과 각 섹션 아래의 **Edit** 단추가 포함되어 있습니다. CNAME 레코드에 대해 **Host Alias (CNAME Records)** 섹션을 사용합니다.

    ![고급 DNS 페이지 업데이트][고급 DNS 페이지 업데이트]

5.  **Edit** 단추를 클릭하면 기존 레코드를 수정하거나 새로운 레코드를 추가할 수 있는 양식이 표시됩니다.

    CNAME 레코드를 추가할 때 **Alias** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Other host** 필드 옆의 원형 필드를 선택하고 **Other host**를 Azure 웹 사이트에 사용한 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다. 이 필드는 Azure 웹 사이트에 사용할 CNAME 레코드를 만들 때 필요하지 않으므로 **Refers to Host Name**을 **Select**로 두십시오.

    ![CNAME 양식][CNAME 양식]

6.  레코드 추가 또는 수정을 완료하면 **Continue**를 클릭하여 변경 내용을 검토합니다. **Save changes only**를 선택하여 변경 내용을 저장합니다.

## <a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용

[WACOM.INCLUDE [modes][2]]

  [Network Solutions]: /ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.networksolutions.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [표준 모드에 대해 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에 트래픽 관리자 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [DNS 편집 페이지]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [Edit Advanced DNS Records가 강조 표시되어 있는 도메인 이름 페이지]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [고급 DNS 페이지 업데이트]: ./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png
  [CNAME 양식]: ./media/web-sites-custom-domain-name/ns-cnametm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
