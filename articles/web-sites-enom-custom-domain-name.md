<properties title="Learn how to configure an Azure web site to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(eNom)
=========================================================

[사용자 지정 도메인](/ko-kr/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인")[GoDaddy](/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/ko-kr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[웹 사이트](/ko-kr/documentation/articles/web-sites-enom-custom-domain-name/ "웹 사이트") | [트래픽 관리자를 사용하는 웹 사이트](/ko-kr/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [eNom](https://enom.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인과 Azure 웹 사이트를 연결하려면 eNom에서 제공하는 도구를 사용하여 DNS 테이블에서 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. enom.com용 DNS 도구를 찾으려면 다음 단계를 따르십시오.

1.  사용 중인 계정으로 eNom에 로그인하고 **Domains**를 클릭한 후 **My Domains**를 클릭합니다. 그러면 도메인 이름이 표시됩니다.

2.  **My Domains** 페이지에서 **Manage Domain** 필드를 사용하여 **Host Records**를 선택합니다. 그러면 호스트 레코드 필드가 표시됩니다.

    ![DNS Zone File 탭](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  Host Records 편집기에서 **Record Type** 필드를 사용하여 특정 레코드 유형을 선택할 수 있습니다. Azure 웹 사이트에서 **CNAME (Alias)** 또는 **A (Address)** 선택 항목만 사용해야 합니다.

    ![영역 파일 편집기](./media/web-sites-custom-domain-name/e-editrecords.png)

    > [WACOM.NOTE] 영역 파일에 항목을 추가하기 전에 eNom에서 루트 도메인('@') 및 하위 도메인(와일드카드)에 대한 DNS 레코드를 이미 만든 것을 볼 수 있습니다. 웹 사이트로 루트 도메인을 리디렉션하거나 와일드카드 A 레코드를 사용하려는 경우 새로 만드는 대신 이러한 항목을 수정해야 합니다.

    -   CNAME 레코드를 추가할 때 **Host Name** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름으로 설정해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다.

        > [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
        >
        > -   **www**의 **Alias** 값 및 **&lt;yourwebsitename\>.azurewebsites.net**의 **Other host** 값
        >
        > 또는
        >
        > -   **awverify.www**의 **Alias** 값 및 **awverify.&lt;yourwebsitename\>.azurewebsites.net**의 **Other host** 값
        >
        > 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

    -   A 레코드를 추가할 때 **Host Name** 필드를 **@**(**contoso.com**과 같은 루트 도메인 이름을 나타냄) \*(모든 하위 도메인을 나타내는 와일드카드) 또는 사용할 특정 하위 도메인(예: **www**)으로 설정해야 합니다. **Address** 필드는 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

        > [WACOM.NOTE] A 레코드를 추가할 때 **awverify**의 호스트 및 \*\*awverify.&lt;yourwebsitename\>.azurewebsites.net의 **Points to**를 사용하여 CNAME 레코드도 추가해야 합니다.

4.  레코드 추가 또는 수정을 완료하면 **Save**를 클릭하여 변경 내용을 저장합니다.

웹 사이트에서 도메인 이름 사용
------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

