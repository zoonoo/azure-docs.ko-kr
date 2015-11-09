<properties
	pageTitle="Azure 앱 서비스에서 사용자 지정 도메인 이름 구성"
	description="Azure 앱 서비스에서 웹 앱으로 사용자 지정 도메인 이름을 사용하는 방법에 대해 알아봅니다."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="cephalin"/>

# Azure 앱 서비스에서 사용자 지정 도메인 이름 구성

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

웹 앱을 만들면 Azure에서 azurewebsites.net의 하위 도메인에 할당됩니다. 예를 들어 웹 앱의 이름이 **contoso**인 경우 URL은 **contoso.azurewebsites.net**입니다. Azure는 가상 IP 주소도 할당합니다.

프로덕션 웹앱의 경우 사용자에게 사용자 지정 도메인 이름을 표시할 수 있습니다. 이 문서에서는 [앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)으로 사용자 지정 도메인을 구성하는 방법에 대해 설명합니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 클릭합니다.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## 개요

외부 도메인 이름에 아직 등록하지 않은 경우(즉, *.azurewebsites.net이 아님) 사용자 지정 도메인을 설정하는 가장 쉬운 방법은 [Azure Preview 포털](https://portal.azure.com)에서 직접 도메인을 구입하는 것입니다. 프로세스를 사용하면 관리할 GoDaddy 등의 타사 사이트로 이동하는 대신 포털에서 직접 웹앱의 도메인 이름을 관리할 수 있습니다. 마찬가지로 웹앱에서 도메인 이름을 구성하는 것은 웹앱이 [Azure 트래픽 관리자](web-sites-traffic-manager-custom-domain-name.md)를 사용하는지 여부와 무관하게 매우 간소화됩니다. 자세한 내용은 [Azure 앱 서비스에서 사용자 지정 도메인 이름 구입 및 구성](custom-dns-web-site-buydomains-web-app.md)을 참조하세요.

도메인 이름이 이미 있거나 다른 도메인 등록 기관에서 도메인을 예약하려는 경우 웹앱에서 사용자 지정 도메인 이름을 가져오는 일반적인 단계는 다음과 같습니다([GoDaddy.com의 특정 지침](web-sites-godaddy-custom-domain-name.md) 참조):

1. 도메인 이름을 예약합니다. 이 문서에서는 해당 과정을 다루지 않습니다. 여러 등록 기관 중에서 선택할 수 있습니다. 가입하면 해당 사이트에서 프로세스를 안내해 줍니다.
1. 도메인을 Azure 웹 앱에 매핑하는 DNS 레코드를 만듭니다.
1. 도메인 이름을 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715) 내에 추가합니다.

이 기본적인 개요 내에서 고려해야 할 특정 사례가 있습니다.

- 루트 도메인을 매핑합니다. 루트 도메인은 도메인 등록 기관을 통해 예약한 도메인입니다. 예를 들어 **contoso.com**입니다.
- 하위 도메인을 매핑합니다. 예를 들어 **blogs.contoso.com**입니다. 다른 웹 앱에 여러 하위 도메인을 매핑할 수 있습니다.
- 와일드카드를 매핑합니다. 예를 들어, ***.contoso.com**입니다. 와일드카드 항목은 도메인의 모든 하위 도메인에 적용됩니다.

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes.md)]


## DNS 레코드 유형

DNS(Domain Name System)는 데이터 레코드를 사용하여 도메인 이름을 IP 주소에 매핑합니다. 여러 가지 유형의 DNS 레코드가 있습니다. 웹 앱의 경우, *A* 레코드 또는 *CNAME* 레코드를 만듭니다.

- A**(주소)** 레코드는 도메인 이름을 IP 주소에 매핑합니다.
- **CNAME(정식 이름)** 레코드는 도메인 이름을 다른 도메인 이름에 매핑합니다. DNS는 두 번째 이름을 사용하여 주소를 조회합니다. 사용자의 브라우저에는 여전히 첫 번째 도메인 이름이 표시됩니다. 예를 들어 contoso.com을 *&lt;yourwebapp&gt;*.azurewebsites.net에 매핑할 수 있습니다.

IP 주소가 변경되는 경우 CNAME 항목은 여전히 유효한 반면 A 레코드는 업데이트되어야 합니다. 하지만 일부 도메인 등록 기관은 루트 도메인 또는 와일드카드 도메인에 대해 CNAME 레코드를 허용하지 않습니다. 이 경우에는 A 레코드를 사용해야 합니다.

> [AZURE.NOTE]웹 앱을 삭제하고 다시 만들거나 웹 앱 모드를 무료로 다시 변경할 경우 IP 주소가 변경될 수 있습니다.


## 가상 IP 주소 찾기

CNAME 레코드를 만들 경우에는 이 단계를 건너뛰십시오. A 레코드를 만들려면 웹 앱의 가상 IP 주소가 필요합니다. IP 주소를 가져오려면

1.	브라우저에서 [Azure 포털](https://portal.azure.com)을 엽니다.
2.	페이지의 왼쪽에서 **찾아보기** 옵션을 클릭합니다.
3.	**웹앱** 블레이드를 클릭합니다.
4.	웹 앱의 이름을 클릭 합니다.
5.	**필수** 페이지에서 **모든 설정**을 클릭합니다.
6.	**사용자 지정 도메인 및 SSL**을 클릭합니다.
7.	**사용자 지정 도메인 및 SSL** 블레이드에서 **외부 도메인 가져오기**를 클릭합니다. IP 주소는 이 부분의 맨 아래에 있습니다.

## DNS 레코드 만들기

도메인 등록 기관에 로그인하고 해당 도구를 사용하여 A 레코드 또는 CNAME 레코드를 추가합니다. 모든 등록 기관의 웹 앱은 조금씩 다르지만, 몇 가지 일반적인 가이드라인이 있습니다.

1.	DNS 레코드를 관리하기 위한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 종종 링크는 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 찾을 수 있습니다.
2.	관리 페이지를 찾았으면 DNS 레코드를 추가하거나 편집할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급** 구성 링크로 나열될 수 있습니다.

페이지는 A 레코드 및 CNAME 레코드를 따로 나열하거나 레코드 유형을 선택할 수 있는 드롭다운을 제공합니다. 또한 A 레코드가 아닌 **IP 주소 레코드** 또는 CNAME 레코드가 아닌 **별칭 레코드**와 같은 다른 이름으로 레코드 유형에 사용할 수도 있습니다. 일반적으로 등록 기관에서 일부 레코드를 만들어 주기 때문에 루트 도메인 또는 공통 하위 도메인에 대해서는 **www**와 같은 레코드가 이미 있을 수 있습니다.

레코드를 만들거나 편집할 때 이 필드에서 도메인 이름을 IP 주소(A 레코드의 경우) 또는 다른 도메인(CNAME 레코드의 경우)에 매핑할 수 있습니다. CNAME 레코드의 경우, 사용자 지정 도메인*에서* azurewebsites.net 하위 도메인*으로* 매핑합니다.

많은 등록 기관 도구에서는 전체 도메인 이름이 아니라 도메인의 하위 도메인 부분만 입력하면 됩니다. 또한 많은 도구가 ‘@’을 사용하여 루트 도메인을 의미합니다. 예를 들면 다음과 같습니다.

<table cellspacing="0" border="1">
  <tr>
    <th>호스트</th>
    <th>레코드 유형</th>
    <th>IP 주소 또는 URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A(주소)</td>
    <td>168.62.48.183</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME(별칭)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

사용자 지정 도메인 이름이 ‘contoso.com’이라고 가정하면 다음과 같은 레코드가 만들어집니다.

- 168\.62.48.183에 매핑된 **contoso.com**
- **contoso.azurewebsites.net**에 매핑된 **www.contoso.com**

>[AZURE.NOTE]Azure DNS를 사용하여 웹앱에 필요한 도메인 레코드를 호스트할 수 있습니다. 사용자 지정 도메인을 구성하고 사용자 레코드를 만들려면 Azure DNS에서 [웹앱에 대한 사용자 지정 DNS 레코드 만들기](../dns-web-sites-custom-domain)를 참조하세요.

<a name="awverify" />
## awverify 레코드 만들기(A 레코드 전용)

A 레코드를 만들면 웹 앱에서 특수 CNAME 레코드도 필요하며, 이 레코드는 사용하려는 도메인을 소유하고 있는지를 확인하는 데 사용됩니다. 이 CNAME 레코드에는 다음과 같은 형식이 있어야 합니다.

- *A 레코드가 루트 도메인 또는 와일드카드 도메인에 매핑된 경우:* **awverify.&lt;yourdomain&gt;**에서 **awverify.&lt;yourwebappname&gt;.azurewebsites.net**으로 매핑되는 CNAME 레코드를 만듭니다. 예를 들어 A 레코드가 **contoso.com**을 위한 것일 경우 **awverify.contoso.com**에 대한 CNAME 레코드를 만듭니다.
- *A 레코드가 특정 하위 도메인에 매핑된 경우:* **awverify.&lt;yourdomain&gt;**에서 **awverify.&lt;yourwebappname&gt;.azurewebsites.net**으로 매핑되는 CNAME 레코드를 만듭니다. 예를 들어 A 레코드가 **blogs.contoso.com**을 위한 것일 경우 **awverify.blogs.contoso.com**에 대한 CNAME 레코드를 만듭니다.

웹 앱 방문자에게는 awverify 하위 도메인이 표시되지 않으며 Azure에서 도메인을 확인하는 데만 사용됩니다.

## 웹 앱에서 도메인 이름 사용

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## DNS 적용 확인

구성 단계를 완료하고 나면 DNS 공급자에 따라 변경 내용이 적용되는 데 다소 시간이 걸릴 수 있습니다. [http://digwebinterface.com/](http://digwebinterface.com/)을 사용하여 예상대로 DNS 적용이 작동하는지 확인할 수 있습니다. 사이트를 찾은 후 텍스트 상자에 호스트 이름을 지정하고 **Dig**를 클릭합니다. 최근 변경 내용이 적용되었는지 확인하려면 결과를 확인합니다.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE]DNS 항목이 적용되는 데는 최대 48시간이 걸립니다(더 걸리는 경우도 있음). 모든 항목이 올바르게 구성된 경우 성공적으로 적용될 때까지 기다려야 합니다.

## 다음 단계

자세한 내용은 [Azure DNS 시작](../dns/dns-getstarted-create-dnszone.md) 및 [Azure DNS로 도메인 위임](../dns/dns-domain-delegation.md)을 참조하세요.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=Nov15_HO1-->