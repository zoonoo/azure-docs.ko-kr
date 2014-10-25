<properties title="Learn how to configure an Azure website to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure website to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name/" title="웹 사이트" class="current">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][]]

[WACOM.INCLUDE [intro][]]

이 문서에서는 Azure 웹 사이트 및 [DirectNic.com][]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [introfooter][]]

이 문서의 내용

-   [DNS 레코드 이해][]
-   [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성][]
-   [사용자 지정 도메인에 대한 DNS 레코드 추가][]
-   [웹 사이트에서 도메인 사용][]

## <a name="understanding-records"></a>DNS 레코드 이해

[WACOM.INCLUDE [understandingdns][]]

## <a name="bkmk_configsharedmode"></a>기본, 공유 또는 표준 모드에 대한 웹 사이트 구성

[WACOM.INCLUDE [modes][]]

<a name="bkmk_configurecname"></a>

## 사용자 지정 도메인에 대한 DNS 레코드 추가

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Directnic에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Directnic.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 Directnic.com에 로그인하고 **My Services**를 클릭한 후 **Domains**을 클릭합니다.

    ![Directnic 서비스 메뉴][]

2.  Azure 웹 사이트에서 사용할 도메인 이름을 클릭합니다.

3.  도메인 관리 페이지의 **Services** 창에서 **DNS**의 **Manage** 단추를 클릭합니다.

    ![서비스 패널][]

4.  **Type**, **Name** 및 **Data** 필드를 작성하여 DNS 레코드를 추가합니다. 이 작업을 완료하면 **Add Record** 단추를 클릭합니다.

    ![][]

    -   CNAME 레코드를 추가할 때 **Name** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Data** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

    -   A 레코드를 추가할 때 **Name** 필드를 **@** (**contoso.com**과 같은 루트 도메인 이름을 나타냄) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Data** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

        > [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
        >
        > -   **www**의 **Name** 값 및 **\<yourwebsitename\>.azurewebsites.net**의 **Data** 값
        >
        > 또는
        >
        > -   **awverify.www**의 **Name** 값 및 **awverify.\<yourwebsitename\>.azurewebsites.net**의 **Data** 값
        >
        > 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

## <a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용

[WACOM.INCLUDE [modes][1]]

  [사용자 지정 도메인]: /ko-KR/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인"
  [GoDaddy]: /ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ko-KR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ko-KR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ko-KR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ko-KR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [웹 사이트]: /ko-KR/documentation/articles/web-sites-directnic-custom-domain-name/ "웹 사이트"
  [트래픽 관리자를 사용하는 웹 사이트]: /ko-KR/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [DirectNic.com]: https://directnic.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에서 도메인 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Directnic 서비스 메뉴]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [서비스 패널]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  []: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png
  [1]: ../includes/custom-dns-web-site-enable-on-web-site.md
