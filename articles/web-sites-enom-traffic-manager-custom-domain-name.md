<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# 트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/ko-KR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-KR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-KR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ko-KR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ko-KR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ko-KR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ko-KR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-KR/documentation/articles/web-sites-enom-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/ko-KR/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [eNom][eNom]에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 eNom에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. enom.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 eNom에 로그인하고 **Domains**를 클릭한 후 **My Domains**를 클릭합니다. 그러면 도메인 이름이 표시됩니다.

2.  **My Domains** 페이지에서 **Manage Domain** 필드를 사용하여 **Host Records**를 선택합니다. 그러면 호스트 레코드 필드가 표시됩니다.

    ![DNS Zone File 탭][DNS Zone File 탭]

3.  Host Records 편집기에서 **Record Type** 필드를 사용하여 특정 레코드 유형을 선택할 수 있습니다. 트래픽 관리자를 사용하는 Azure 웹 사이트의 경우 트래픽 관리자에서만 CNAME 레코드를 처리하므로 **CNAME (Alias)** 선택 항목만 사용해야 합니다.

    ![영역 파일 편집기][영역 파일 편집기]

    -   CNAME 레코드를 추가할 때 **Host Name** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트에 사용한 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다.

4.  레코드 추가 또는 수정을 완료하면 **Save**를 클릭하여 변경 내용을 저장합니다.

## <a name="enabledomain"></a>트래픽 관리자 웹 사이트 사용

[WACOM.INCLUDE [modes][1]]

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [eNom]: https://enom.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS 레코드 이해]: #understanding-records
  [표준 모드에 대해 웹 사이트 구성]: #bkmk_configsharedmode
  [사용자 지정 도메인에 대한 DNS 레코드 추가]: #bkmk_configurecname
  [웹 사이트에 트래픽 관리자 사용]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [DNS Zone File 탭]: ./media/web-sites-custom-domain-name/e-hostrecords.png
  [영역 파일 편집기]: ./media/web-sites-custom-domain-name/e-editrecordstm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
