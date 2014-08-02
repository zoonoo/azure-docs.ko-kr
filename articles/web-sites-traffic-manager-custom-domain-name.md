<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

트래픽 관리자를 사용하는 Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성
============================================================================

[사용자 지정 도메인](/en-us/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[웹 사이트](/en-us/documentation/articles/web-sites-custom-domain-name/ "웹 사이트") | [트래픽 관리자를 사용하는 웹 사이트](/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 부하 분산을 위해 트래픽 관리자를 사용하는 Azure 웹 사이트에서 사용자 지정 도메인 이름을 사용하는 일반 지침을 제공합니다. 이 문서의 맨 위에 있는 탭에 도메인 등록 기관이 나열되는지 확인하십시오. 나열될 경우 등록 기관별 단계를 보려면 해당 탭을 선택하십시오.

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

사용자 지정 도메인을 Azure 웹 사이트에 연결하려면 도메인 이름을 구입한 도메인 등록 기관에서 제공하는 도구를 사용하여 DNS 테이블에 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 사용하십시오.

1.  도메인 등록 기관에서 계정에 로그온하고 DNS 레코드 관리에 대한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 종종 이 페이지에 대한 링크는 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 찾을 수 있습니다.

2.  도메인 이름에 대한 관리 페이지를 찾았으면 DNS 레코드를 편집할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급** 구성 링크로 나열될 수 있습니다.

    -   페이지에는 '**@**' 또는 '\*'를 '도메인 파킹' 페이지에 연결하는 항목과 같은 몇 가지 레코드가 이미 만들어져 있을 수 있습니다. 또한 **www**와 같은 일반 하위 도메인에 대한 레코드가 포함될 수 있습니다.
    -   페이지는 **CNAME 레코드**를 나타내거나 레코드 유형을 선택할 수 있는 드롭다운을 제공합니다. **A records** 및 **MX 레코드**와 같은 다른 레코드를 나타낼 수도 있습니다. 어떤 경우에는 CNAME 레코드를 **별칭 레코드**와 같은 다른 이름으로 부릅니다.
    -   또한 페이지에는 **호스트 이름** 또는 **도메인 이름**에서 다른 도메인 이름으로 **매핑**할 수 있는 필드가 있습니다.

3.  각 등록 기관에 대한 구체적인 설명은 다르지만 일반적으로 사용자 지정 도메인 이름(예: **contoso.com**)*에서* Azure 웹 사이트에 사용하는 트래픽 관리자 도메인 이름(**contoso.azurewebsites.net**)*으로* 매핑합니다.

4.  등록 기관에서 DNS 레코드를 추가하거나 수정했으면 변경 내용을 저장합니다.

트래픽 관리자 웹 사이트 사용
----------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

