<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Moniker)
============================================================

[사용자 지정 도메인](/ko-kr/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인")[GoDaddy](/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/ko-kr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[웹 사이트](/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name/ "웹 사이트") | [트래픽 관리자를 사용하는 웹 사이트](/ko-kr/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Moniker.com](https://moniker.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [DNS 레코드 이해](#understanding-records)
-   [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성](#bkmk_configsharedmode)
-   [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
-   [웹 사이트에서 도메인 사용](#enabledomain)

DNS 레코드 이해
---------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

기본, 공유 또는 표준 모드에 대한 웹 사이트 구성
-----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## 사용자 지정 도메인에 대한 DNS 레코드 추가 

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 Moniker에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. Moniker.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 Moniker.com에 로그인하고 **My Domains**를 클릭한 후 **Manage Templates**를 클릭합니다.

    ![Moniker의 My Domains 페이지](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  **Zone Template Management** 페이지에서 **Create New Template**을 선택합니다.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  **Template Name**을 입력합니다.

4.  그런 다음 **Record Type**을 먼저 선택하여 DNS 레코드를 만듭니다. 그런 다음 **Hostname** 및 **Address**를 입력합니다.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   CNAME 레코드를 추가할 때 **Hostname** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

    -   A 레코드를 추가할 때 **Hostname** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄) 또는 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

        > [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
        >
        > -   **www**의 **Hostname** 값 및 **&lt;yourwebsitename\>.azurewebsites.net**의 **Address** 값
        >
        > 또는
        >
        > -   **awverify.www**의 **Hostname** 값 및 **awverify.&lt;yourwebsitename\>.azurewebsites.net**의 **Address** 값
        >
        > 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

5.  **Add** 단추를 클릭하여 항목을 추가합니다.

6.  모든 항목을 추가한 후 **Save** 단추를 클릭합니다.

7.  **Domain Manager**를 선택하여 도메인 목록으로 돌아갑니다.

8.  대상 도메인의 확인란을 선택하고 **Manage Templates**를 다시 클릭합니다.

9.  이전 단계에서 만든 새로운 템플릿을 찾습니다. 그런 다음 **place selected domains (1) into this Template** 링크를 클릭합니다.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

웹 사이트에서 도메인 이름 사용
------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

