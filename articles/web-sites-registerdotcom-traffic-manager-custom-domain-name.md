<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# 트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

이 문서에서는 Azure 웹 사이트 및 [Register.com][1]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [tmwebsitefooter][]]

[WACOM.INCLUDE [introfooter][]]

이 문서의 내용

-   [DNS 레코드 이해][]
-   [표준 모드에 대해 웹 사이트 구성][]
-   [사용자 지정 도메인에 대한 DNS 레코드 추가][]
-   [웹 사이트에 트래픽 관리자 사용][]

## <a name="understanding-records"></a>DNS 레코드 이해

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>표준 모드에 대해 웹 사이트 구성

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## 사용자 지정 도메인에 대한 DNS 레코드 추가

</p>
사용자 지정 도메인을 Azure 웹 사이트에 연결하려면 Register.com에서 제공하는 도구를 사용하여 DNS 테이블에 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 사용하세요.

1.  register.com에서 사용 중인 계정에 로그온하고 오른쪽 위 모서리에서 **Your Account**를 클릭하여 도메인을 본 후 사용자 지정 도메인 이름을 선택합니다.

    ![내 계정 페이지][]

2.  **Advanced Technical Settings**가 보일 때까지 페이지 아래로 이동합니다. 이 섹션의 링크에서 도메인에 대한 레코드를 관리할 수 있습니다. CNAME 레코드의 경우 **Edit Domain Aliases Records** 링크를 사용합니다.

    ![ADVANCED TECHNICAL SETTINGS][]

3.  **Edit** 단추를 클릭하면 기존 레코드를 수정하거나 새로운 레코드를 추가할 수 있는 양식이 표시됩니다. CNAME 및 A 레코드 모두의 양식은 서로 유사합니다.

    -   CNAME 레코드를 추가할 때 **.mydomainname.com** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름을 가리키는 **points to** 값을 선택해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다. 이 필드는 Azure 웹 사이트에 사용할 CNAME 레코드를 만들 때 필요하지 않으므로 **Refers to Host Name**을 **Select**로 두십시오.

        ![CNAME 양식][]

4.  레코드 추가 또는 수정을 완료하면 **Continue**를 클릭하여 변경 내용을 검토합니다. **Continue**를 다시 선택하여 변경 내용을 저장합니다.

## <a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용

[WACOM.INCLUDE [modes][2]]

  [사용자 지정 도메인]: /ko-KR/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인"
  [GoDaddy]: /ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ko-KR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ko-KR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ko-KR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ko-KR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [웹 사이트]: /ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "웹 사이트"
  [트래픽 관리자를 사용하는 웹 사이트]: /ko-KR/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.register.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [표준 모드에 대해 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에 트래픽 관리자 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [내 계정 페이지]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [ADVANCED TECHNICAL SETTINGS]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png
  [CNAME 양식]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
