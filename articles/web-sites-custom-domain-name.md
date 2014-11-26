<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson" />

# Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name" title="사용자 지정 도메인" class="current">사용자 지정 도메인</a><a href="/ko-kr/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ko-kr/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ko-kr/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ko-kr/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ko-kr/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ko-kr/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ko-kr/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ko-kr/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/web-sites-custom-domain-name/" title="웹 사이트" class="current">웹 사이트</a> | <a href="/ko-kr/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="트래픽 관리자를 사용하는 웹 사이트">트래픽 관리자를 사용하는 웹 사이트</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

웹 사이트를 만들면 Azure에서 azurewebsites.net의 하위 도메인에 할당됩니다. 예를 들어 웹 사이트의 이름이 **contoso**인 경우 URL은 **contoso.azurewebsites.net**입니다. Azure는 가상 IP 주소도 할당합니다.

![contoso.azurewebsites.net subdomain][contoso.azurewebsites.net subdomain]

프로덕션 웹 사이트의 경우 사용자에게 사용자 지정 도메인 이름을 표시하고자 할 것입니다. 이 문서에서는 Azure 웹 사이트에서 사용자 지정 도메인을 구성하는 방법을 설명합니다. (이 문서는 도메인 등록 기관을 위한 일반적인 지침을 제공합니다. 이 문서의 맨 위에 있늩 탭은 특정 등록 기관의 일부 문서로 연결됩니다.)

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

이 문서의 내용

-   [개요][개요]
-   [DNS 레코드 유형][DNS 레코드 유형]
-   [가상 IP 주소 찾기][가상 IP 주소 찾기]
-   [DNS 레코드 만들기][DNS 레코드 만들기]
-   [“awverify” 레코드 만들기(A 레코드 전용)][“awverify” 레코드 만들기(A 레코드 전용)]
-   [웹 사이트에서 도메인 이름 사용][웹 사이트에서 도메인 이름 사용]

## 개요

다음은 사용자 지정 도메인 이름을 구성하기 위한 일단적인 단계입니다.

1.  도메인 이름을 예약합니다. 이 문서에서는 해당 과정을 다루지 않습니다. 여러 등록 기관 중에서 선택할 수 있습니다. 가입하면 해당 사이트에서 프로세스를 안내해 줍니다.
2.  도메인을 Azure 웹 사이트에 매핑하는 DNS 레코드를 만듭니다.
3.  Azure 관리 포털 안에 도메인 이름을 추가합니다.

이 기본적인 개요 내에서 고려해야 할 특정 사례가 있습니다.

-   루트 도메인을 매핑합니다. 루트 도메인은 도메인 등록 기관을 통해 예약한 도메인입니다. 예를 들어 **contoso.com**입니다.
-   하위 도메인을 매핑합니다. 예를 들어 **blogs.contoso.com**입니다. 서로 다른 하위 도메인을 서로 다른 웹 사이트에 매핑할 수 있습니다.
-   와일드카드를 매핑합니다. 예를 들어 \***.contoso.com**입니다. 와일드카드 항목은 도메인의 모든 하위 도메인에 적용됩니다.

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## DNS 레코드 유형

DNS(Domain Name System)는 데이터 레코드를 사용하여 도메인 이름을 IP 주소에 매핑합니다. 여러 가지 유형의 DNS 레코드가 있습니다. 웹 사이트의 경우 *A* 레코드 또는 *CNAME* 레코드를 만듭니다.

-   A**(주소)** 레코드는 도메인 이름을 IP 주소에 매핑합니다.
-   **CNAME(정식 이름)** 레코드는 도메인 이름을 다른 도메인 이름에 매핑합니다. DNS는 두 번째 이름을 사용하여 주소를 조회합니다. 사용자의 브라우저에는 여전히 첫 번째 도메인 이름이 표시됩니다. 예를 들어 contoso.com을 *\<yoursite\>*.azurewebsites.net에 매핑할 수 있습니다.

IP 주소가 변경되는 경우 CNAME 항목은 여전히 유효한 반면 A 레코드는 업데이트되어야 합니다. 하지만 일부 도메인 등록 기관은 루트 도메인 또는 와일드카드 도메인에 대해 CNAME 레코드를 허용하지 않습니다. 이 경우에는 A 레코드를 사용해야 합니다.

> [WACOM.NOTE] 웹 사이트를 삭제하고 다시 만들거나 웹 사이트 모드를 무료로 다시 변경할 경우 IP 주소가 변경될 수 있습니다.

## 가상 IP 주소 찾기

CNAME 레코드를 만들 경우에는 이 단계를 건너뛰십시오. A 레코드를 만들려면 웹 사이트의 가상 IP 주소가 필요합니다. IP 주소를 가져오려면

1.  브라우저에서 [Azure 관리 포털][Azure 관리 포털]을 엽니다.
2.  **웹 사이트** 탭에서 사이트의 이름을 클릭하고 **대시보드**를 선택합니다.
3.  페이지 아래쪽에서 **도메인 관리**를 선택합니다. (이 옵션이 해제된 경우에는 공유, 기본 또는 표준 모드를 사용 중인지 확인하십시오. 자세한 내용은 [웹 사이트 크기를 조정하는 방법][웹 사이트 크기를 조정하는 방법](영문)을 참조하십시오.)

    ![][0]

4.  IP 주소가 대화 상자 맨 아래에 나열됩니다.

    ![][1]

## DNS 레코드 만들기

도메인 등록 기관에 로그인하고 해당 도구를 사용하여 A 레코드 또는 CNAME 레코드를 추가합니다. 모든 등록 기관의 웹 사이트는 조금씩 다르지만, 몇 가지 일반적인 가이드라인이 있습니다.

1.  DNS 레코드를 관리하기 위한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 종종 링크는 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 찾을 수 있습니다.
2.  관리 페이지를 찾았으면 DNS 레코드를 추가하거나 편집할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급** 구성 링크로 나열될 수 있습니다.

페이지는 A 레코드 및 CNAME 레코드를 따로 나열하거나 레코드 유형을 선택할 수 있는 드롭다운을 제공합니다. 또한 A 레코드가 아닌 **IP 주소 레코드** 또는 CNAME 레코드가 아닌 **별칭 레코드**와 같은 다른 이름으로 레코드 유형에 사용할 수도 있습니다. 일반적으로 등록 기관에서 일부 레코드를 만들어 주기 때문에 루트 도메인 또는 공통 하위 도메인에 대해서는 **www**와 같은 레코드가 이미 있을 수 있습니다.

레코드를 만들거나 편집할 때 이 필드에서 도메인 이름을 IP 주소(A 레코드의 경우) 또는 다른 도메인(CNAME 레코드의 경우)에 매핑할 수 있습니다. CNAME 레코드의 경우, 사용자 지정 도메인*에서* azurewebsites.net 하위 도메인*으로* 매핑합니다.

많은 등록 기관 도구에서는 전체 도메인 이름이 아니라 도메인의 하위 도메인 부분만 입력하면 됩니다. 또한 많은 도구가 ‘@’을 사용하여 루트 도메인을 의미합니다. 예를 들면 다음과 같습니다.

| 호스트 | 레코드 유형 | IP 주소 또는 URL          |
|--------|-------------|---------------------------|
| @      | A(주소)     | 127.0.0.1                 |
| www    | CNAME(별칭) | contoso.azurewebsites.net |

사용자 지정 도메인 이름이 ‘contoso.com’이라고 가정하면 다음과 같은 레코드가 만들어집니다.

-   127.0.0.1에 매핑된 **contoso.com**
-   **contoso.azurewebsites.net**에 매핑된 **www.contoso.com**

## “awverify” 레코드 만들기(A 레코드 전용)

A 레코드를 만들면 Azure 웹 사이트에서 특수 CNAME 레코드도 필요로 합니다. 이 레코드는 사용하려는 도메인을 소유하고 있는지를 확인하는 데 사용됩니다. 이 CNAME 레코드에는 다음과 같은 형식이 있어야 합니다.

-   *A 레코드가 루트 도메인 또는 와일드카드 도메인에 매핑되는 경우:* **awverify.\<yourdomain\>**에서 **awverify.\<yourwebsitename\>.azurewebsites.net**으로 매핑되는 CNAME 레코드를 만듭니다. 예를 들어 A 레코드가 **contoso.com**을 위한 것일 경우 **awverify.contoso.com**에 대한 CNAME 레코드를 만듭니다.
-   *A 레코드가 특정 하위 도메인을 매핑하는 경우:* **awverify.\<subdomain\>**에서 **awverify.\<yourwebsitename\>.azurewebsites.net**으로 매핑되는 CNAME 레코드를 만듭니다. 예를 들어 A 레코드가 **blogs.contoso.com**을 위한 것일 경우 **awverify.blogs.contoso.com**에 대한 CNAME 레코드를 만듭니다.

사이트 방문자에게는 awverify 하위 도메인이 표시되지 않습니다. 이것은 Azure에서 도메인을 확인하는 데만 사용됩니다.

## 웹 사이트에서 도메인 이름 사용

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

 <!-- Images -->

  [contoso.azurewebsites.net subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
  [개요]: #overview
  [DNS 레코드 유형]: #dns-record-types
  [가상 IP 주소 찾기]: #find-the-virtual-ip-address
  [DNS 레코드 만들기]: #create-the-dns-records
  [웹 사이트에서 도메인 이름 사용]: #enable-the-domain-name-on-your-website
  [Azure 관리 포털]: https://manage.windowsazure.com
  [웹 사이트 크기를 조정하는 방법]: http://www.windowsazure.com/ko-kr/documentation/articles/web-sites-scale/
  [0]: media/web-sites-custom-domain-name/dncmntask-cname-6.png
  [1]: media/web-sites-custom-domain-name/ipaddress.png
