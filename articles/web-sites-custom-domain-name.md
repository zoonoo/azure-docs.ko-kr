<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인" class="current">사용자 지정 도메인</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="웹 사이트" class="current">웹 사이트</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트">트래픽 관리자를 사용하는 웹 사이트</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

이 문서에서는 Azure 웹 사이트에서 사용자 지정 도메인 이름을 사용하는 일반 지침을 제공합니다. 이 문서의 맨 위에 있는 탭에 도메인 등록 기관이 나열되는지 확인하십시오. 나열될 경우 등록 기관별 단계를 보려면 해당 탭을 선택하십시오.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서에서는 다음을 수행합니다.

* [DNS 레코드 이해](#understanding-records)
* [기본, 공유 또는 표준 모드에 대한 웹 사이트 구성](#bkmk_configsharedmode)
* [사용자 지정 도메인에 대한 DNS 레코드 추가](#bkmk_configurecname)
* [웹 사이트에서 도메인 사용](#enabledomain)

<h2><a name="understanding-records"></a>DNS 레코드 이해</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>기본, 공유 또는 표준 모드에 대한 웹 사이트 구성</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 DNS 레코드 추가</h2>

사용자 지정 도메인을 Azure 웹 사이트에 연결하려면 도메인 이름을 구입한 도메인 등록 기관에서 제공하는 도구를 사용하여 DNS 테이블에 사용자 지정 도메인에 대한 새 항목을 추가해야 합니다. DNS 도구를 찾아 사용하려면 다음 단계를 사용하십시오.

1.	도메인 등록 기관에서 계정에 로그온하고 DNS 레코드 관리에 대한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 종종 이 페이지에 대한 링크는 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 찾을 수 있습니다.

2.	도메인 이름에 대한 관리 페이지를 찾았으면 DNS 레코드를 편집할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급** 구성 링크로 나열될 수 있습니다.
	* 페이지에는 '**@**' 또는 '\*'를 '도메인 파킹' 페이지에 연결하는 항목과 같은 몇 가지 레코드가 이미 만들어져 있을 수 있습니다. 또한 **www**와 같은 일반 하위 도메인에 대한 레코드가 포함될 수 있습니다.
	* 페이지는 **A 레코드** 및 **CNAME 레코드**를 나타내거나 레코드 유형을 선택할 수 있는 드롭다운을 제공합니다. **MX 레코드**와 같은 다른 레코드를 나타낼 수도 있습니다. 경우에 따라 이러한 레코드는 A 레코드가 아닌 **IP 주소 레코드** 또는 CNAME 레코드가아닌 **별칭 레코드**와 같은 다른 이름으로 불립니다.
	* 또한 페이지에는 **호스트 이름** 또는 **도메인 이름**에서 **IP 주소**나 다른 도메인 이름으로 **매핑**할 수 있는 필드가 있습니다.   
<br>
3.	각 등록 기관의 설명은 다양하지만 일반적으로 사용자 지정 도메인 이름(예: **contoso.com**)*에서* Azure 웹 사이트 도메인 이름(**contoso.azurewebsites.net**)이나 Azure 웹 사이트 가상 IP 주소*로* 매핑됩니다.
    
    * CNAME 레코드는 항상 Azure 웹 사이트 도메인 **contoso.azurewebsites.net**으로 매핑됩니다. 따라서 **www**와 같은 도메인*에서* **<yourwebsitename>.azurewebsites.net** 주소*로* 매핑됩니다.
     
      > [WACOM.NOTE] A 레코드를 사용하는 경우 다음 구성 중 하나를 사용하여 CNAME 레코드도 추가해야 합니다.
      
      > * **www***에서*
      >   **<yourwebsitename>.azurewebsites.net***으로* 매핑합니다.
      > 
      > 또는
      > 
      > * **awverify.www***에서*
      >   **awverify.<yourwebsitename>.azurewebsites.net***으로*
      >   매핑합니다.
      > 
      > 이 CNAME 레코드는 Azure에서 A 레코드가 설명하는 도메인을 개발자가 소유하고 있는지 확인하는 데 사용됩니다.
    
    * A 레코드는 항상 Azure 웹 사이트 가상 IP 주소로 매핑됩니다. 따라서 **www**와 같은 도메인*에서* 웹 사이트의 가상 IP 주소*로* 매핑되는 것입니다.
     
      > [WACOM.NOTE] 루트 도메인(예: **contoso.com**)을 Azure 웹 사이트로 매핑하려면 종종 '**@**' 또는 빈 항목을 가상 IP 주소로 매핑합니다. 모든 하위 도메인을 가상 IP 주소로 매핑하는 와일드카드 매핑을 만들려면 일반적으로 '\*'에서 가상 IP 주소로 매핑합니다.
      > 
      > 루트 또는 와일드카드 매핑에 대한 설명은 등록 기관마다 다릅니다. 보다 자세한 지침은 등록 기관에서 제공하는 설명서를 참조하십시오.

<br>
4.	등록 기관에서 DNS 레코드를 추가하거나 수정했으면 변경 내용을 저장합니다.

<h2><a name="enabledomain"></a>웹 사이트에서 도메인 이름 사용</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]