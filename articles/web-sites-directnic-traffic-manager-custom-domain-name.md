<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [DirectNic.com](https://directnic.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [표준 모드에 대해 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에 트래픽 관리자 사용](#enabledomain)

DNS 레코드 이해
---------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

표준 모드에 대해 웹 사이트 구성
-------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## 사용자 지정 도메인에 대한 DNS 레코드 추가 

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Directnic에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Directnic.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 Directnic.com에 로그인하고 **My Services**를 클릭한 후 **Domains**을 클릭합니다.

    ![Directnic 서비스 메뉴](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Azure 웹 사이트에서 사용할 도메인 이름을 클릭합니다.

3.  도메인 관리 페이지의 **Services** 창에서 **DNS**의 **Manage** 단추를 클릭합니다.

    ![서비스 패널](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  **Type**, **Name** 및 **Data** 필드를 작성하여 DNS 레코드를 추가합니다. 이 작업을 완료하면 **Add Record** 단추를 클릭합니다.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    -   CNAME 레코드를 추가할 때 **Name** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Data** 필드는 Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다.

        > [WACOM.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

트래픽 관리자 웹 사이트 사용
----------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

