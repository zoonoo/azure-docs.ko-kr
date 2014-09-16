<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Moniker, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인">사용자 지정 도메인</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="웹 사이트">웹 사이트</a> | <a href="/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트" class="current">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 Azure 웹 사이트 및 [Moniker](https://moniker.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Moniker에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Moniker.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 Moniker.com에 로그인하고 **My Domains**를 클릭한 후 **Manage Templates**를 클릭합니다.

    ![Moniker의 My Domains 페이지](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  **Zone Template Management** 페이지에서 **Create New Template**을 선택합니다.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  **Template Name**을 입력합니다.

4.  그런 다음 **Record Type**을 먼저 선택하여 DNS 레코드를 만듭니다. 그런 다음 **Hostname** 및 **Address**를 입력합니다.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    -   CNAME 레코드를 추가할 때 **Hostname** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트에 사용 중인 트래픽 관리자 프로필의 **.trafficmanager.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.trafficmanager.net**으로 설정해야 합니다.

        > [WACOM.NOTE] 트래픽 관리자를 사용하여 부하를 분산한 웹 사이트와 사용자 지정 도메인 이름을 연결할 경우에만 CNAME 레코드를 사용해야 합니다.

5.  **Add** 단추를 클릭하여 항목을 추가합니다.

6.  모든 항목을 추가한 후 **Save** 단추를 클릭합니다.

7.  **Domain Manager**를 선택하여 도메인 목록으로 돌아갑니다.

8.  대상 도메인의 확인란을 선택하고 **Manage Templates**를 다시 클릭합니다.

9.  이전 단계에서 만든 새로운 템플릿을 찾습니다. 그런 다음 **place selected domains (1) into this Template** 링크를 클릭합니다.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

트래픽 관리자 웹 사이트 사용
----------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

