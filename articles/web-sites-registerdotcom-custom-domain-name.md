<properties title="Learn how to configure an Azure web site to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성(Register.com)
=================================================================

[사용자 지정 도메인](/ko-kr/documentation/articles/web-sites-custom-domain-name "사용자 지정 도메인")[GoDaddy](/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/ko-kr/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[웹 사이트](/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "웹 사이트") | [트래픽 관리자를 사용하는 웹 사이트](/ko-kr/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "트래픽 관리자를 사용하는 웹 사이트")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트 및 [Register.com](https://register.com)에서 구매한 사용자 지정 도메인 이름 사용에 대한 지침을 제공합니다.

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

사용자 지정 도메인을 Azure 웹 사이트에 연결하려면 Register.com에서 제공하는 도구를 사용하여 DNS 테이블에 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 사용하십시오.

1.  register.com에서 사용 중인 계정에 로그온하고 오른쪽 위 모서리에서 **Your Account**를 클릭하여 도메인을 본 후 사용자 지정 도메인 이름을 선택합니다.

    ![내 계정 페이지](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  **Advanced Technical Settings**가 보일 때까지 페이지 아래로 이동합니다. 이 섹션의 링크에서 도메인에 대한 레코드를 관리할 수 있습니다.

    -   A 레코드의 경우 **Edit IP Address Records** 링크를 사용합니다.
    -   CNAME 레코드의 경우 **Edit Domain Aliases Records** 링크를 사용합니다.

    ![ADVANCED TECHNICAL SETTINGS](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

3.  **Edit** 단추를 클릭하면 기존 레코드를 수정하거나 새로운 레코드를 추가할 수 있는 양식이 표시됩니다. CNAME 및 A 레코드 모두의 양식은 서로 유사합니다.

    -   CNAME 레코드를 추가할 때 **.mydomainname.com** 필드를, 사용할 하위 도메인으로 설정해야 합니다. 예를 들면 **www**로 설정해야 합니다. Azure 웹 사이트의 **.azurewebsites.net** 도메인 이름을 가리키는 **points to** 값을 선택해야 합니다. 예를 들면 **contoso.azurwebsites.net**으로 설정해야 합니다. 이 필드는 Azure 웹 사이트에 사용할 CNAME 레코드를 만들 때 필요하지 않으므로 **Refers to Host Name**을 **Select**로 두십시오.

        ![CNAME 양식](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

        > [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
        >
        > -   **www**의 **Alias** 값 및 **&lt;yourwebsitename\>.azurewebsites.net**의 **Other host** 값
        >
        > 또는
        >
        > -   **awverify.www**의 **Alias** 값 및 **awverify.&lt;yourwebsitename\>.azurewebsites.net**의 **Other host** 값
        >
        > 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.

    -   A 레코드를 추가할 때 **.mydomainname.com** 필드를, 사용할 하위 도메인(예: **www**)으로 설정해야 합니다. 루트 도메인을 설정하려면 필드를 공백으로 두고, 와일드카드 매핑을 만들려면 **\*\*를 사용합니다. \*\*points to** 필드를 Azure 웹 사이트의 IP 주소로 설정해야 합니다.

        ![레코드 양식](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

4.  레코드 추가 또는 수정을 완료하면 **Continue**를 클릭하여 변경 내용을 검토합니다. **Continue**를 다시 선택하여 변경 내용을 저장합니다.

웹 사이트에서 도메인 이름 사용
------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

