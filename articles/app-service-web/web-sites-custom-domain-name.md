<properties
	pageTitle="Azure 앱에 사용자 지정 도메인 이름 매핑"
	description="Azure 앱 서비스에서 앱으로 사용자 지정 도메인 이름(베니티 도메인)을 매핑하는 방법에 대해 알아봅니다."
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
	ms.date="07/27/2016"
	ms.author="cephalin"/>

# Azure 앱에 사용자 지정 도메인 이름 매핑

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

이 문서는 웹앱, 모바일 앱 백 엔드 또는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)의 API 앱에 사용자 지정 도메인 이름을 수동으로 매핑하는 방법을 보여 줍니다.

사용자의 앱에는 이미 azurewebsites.net의 고유한 하위 도메인이 함께 제공되어 있습니다. 예를 들어 앱의 이름이 **contoso**인 경우 해당 도메인 이름은 **contoso.azurewebsites.net**입니다. 그러나 사용자 지정 도메인 이름을 앱에 매핑하여 URL(예: `www.contoso.com`)이 브랜드를 반영하도록 할 수 있습니다.

>[AZURE.NOTE] [Azure 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가로부터 도움을 받으세요. 훨씬 높은 수준의 지원을 받으려면 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기**를 클릭하세요.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Azure 포털에서 새 사용자 지정 도메인 구입

아직 사용자 지정 도메인 이름을 구입하지 않은 경우 이를 구입하고 [Azure 포털](https://portal.azure.com)의 앱 설정에서 직접 관리할 수 있습니다. 앱에서 [Azure 트래픽 관리자](web-sites-traffic-manager-custom-domain-name.md)의 사용 여부에 관계없이 이 옵션을 통해 사용자 지정 도메인을 앱에 손쉽게 매핑할 수 있습니다.

자세한 내용은 [앱 서비스에 대한 사용자 지정 도메인 이름 구입](custom-dns-web-site-buydomains-web-app.md)을 참조하세요.

## 외부에서 구입한 사용자 지정 도메인 매핑

[Azure DNS](https://azure.microsoft.com/services/dns/) 또는 타사 공급자로부터 사용자 지정 도메인을 이미 구입한 경우 세 가지 주요 단계에 따라 사용자 지정 도메인을 앱에 매핑합니다.

1. [*(레코드만)* 앱의 IP 주소를 가져옵니다](#vip).
2. [도메인을 앱에 매핑하는 DNS 레코드를 만듭니다](#createdns).
    - **어디에서**: 도메인 등록 기관의 고유한 관리 도구(예: Azure DNS, GoDaddy 등).
    - **왜**: 도메인 등록 기관에서 Azure 앱에 대해 원하는 사용자 지정 도메인을 확인하는 방법을 알도록 합니다.
1. [Azure 앱에 대해 사용자 지정 도메인 이름을 사용](#enable)합니다.
    - **어디에서**: [Azure 포털](https://portal.azure.com).
    - **왜**: 앱에서 사용자 지정 도메인 이름에 대한 요청에 응답하는 방법을 알도록 합니다.
3. [DNS 전파를 확인합니다](#verify).

### 매핑할 수 있는 도메인 형식

Azure 앱 서비스를 사용하면 다음 범주의 사용자 지정 도메인을 앱에 매핑할 수 있습니다.

- **루트 도메인** - 도메인 등록 기관을 통해 예약한 도메인 이름입니다(일반적으로 `@` 호스트 레코드로 표시). 예를 들어 **contoso.com**입니다.
- **하위 도메인** - 루트 도메인 아래에 있는 모든 도메인입니다. 예를 들어 **www.contoso.com**(`www` 호스트 레코드로 표시됨)입니다. 동일한 루트 도메인의 다양한 하위 도메인을 Azure의 다양한 앱에 매핑할 수 있습니다.
- **와일드 카드 도메인** - [가장 왼쪽의 DNS 레이블이 `*`인 하위 도메인입니다](https://en.wikipedia.org/wiki/Wildcard_DNS_record)(예: 호스트 레코드 `*` 및 `*.blogs`). 예를 들어, ***.contoso.com**입니다.

### 사용할 수 있는 DNS 레코드 유형

필요에 따라 두 가지 서로 다른 표준 DNS 레코드 유형을 사용하여 사용자 지정 도메인을 매핑할 수 있습니다.

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - 사용자 지정 도메인 이름을 Azure 앱의 가상 IP 주소로 직접 매핑합니다.
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - 사용자 지정 도메인 이름을 앱의 Azure 도메인 이름 **&lt;*appname*>.azurewebsites.net**으로 매핑합니다.

CNAME의 장점은 IP 주소 변경 간에도 유지된다는 점입니다. 앱을 삭제하고 다시 만들거나 높은 가격 책정 계층에서 **공유** 계층으로 다시 변경하는 경우 앱의 가상 IP 주소가 변경될 수 있습니다. 이러한 변경을 통해 CNAME 레코드는 여전히 유효한 반면 A 레코드는 업데이트되어야 합니다.

이 자습서는 A 레코드 및 CNAME 레코드를 사용하는 단계를 보여 줍니다.

>[AZURE.IMPORTANT] 루트 도메인에 대한 CNAME 레코드를 만들지 마세요(즉, "루트 레코드"). 자세한 내용은 [루트 도메인에서 CNAME 레코드를 사용할 수 없는 이유](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)를 참조하세요. 루트 도메인을 Azure 앱에 매핑하려면 대신 A 레코드를 사용하세요.

<a name="vip"></a>
## 1단계. *(A 레코드만)* 앱의 IP 주소 가져오기
A 레코드를 사용하여 사용자 지정 도메인 이름을 매핑하려면 Azure 앱의 IP 주소가 필요합니다. 그렇지 않고 CNAME 레코드를 사용하여 매핑하는 경우 이 단계를 건너뛰고 다음 섹션으로 이동합니다.

1.	[Azure 포털](https://portal.azure.com)에 로그인합니다.

2.	왼쪽 메뉴에서 **앱 서비스**를 클릭합니다.

4.	앱을 클릭한 후 **설정** > **사용자 지정 도메인 및 SSL** > **외부 도메인 가져오기**를 클릭합니다.

6.  나중에 사용하기 위해 IP 주소를 기록해 둡니다.

    ![A 레코드로 사용자 지정 도메인 이름 매핑: Azure 앱 서비스 앱의 IP 주소 가져오기](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  이 포털 블레이드를 열어 둡니다. DNS 레코드를 만든 후에 여기로 다시 돌아옵니다.

<a name="createdns"></a>
## 2단계. DNS 레코드 만들기

도메인 등록 기관에 로그인하고 해당 도구를 사용하여 A 레코드 또는 CNAME 레코드를 추가합니다. 각 등록 기관의 UI는 약간 다르므로 공급자의 설명서를 참조해야 합니다. 하지만 다음은 몇 가지 일반적인 지침입니다.

1.	DNS 레코드를 관리하기 위한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 종종 링크는 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 찾을 수 있습니다.
2.	DNS 레코드를 추가하거나 편집할 수 있는 링크를 찾습니다. 이 링크는 **영역 파일**, **DNS 레코드** 또는 **고급** 구성 링크로 나열될 수 있습니다.
3.  레코드를 만들고 변경 내용을 저장합니다.
    - [A 레코드에 대한 지침이 여기에 나와 있습니다](#a).
    - [CNAME 레코드에 대한 지침이 여기에 나와 있습니다](#cname).

<a name="a"></a>
### A 레코드 만들기

A 레코드를 사용하여 Azure 앱의 IP 주소에 매핑하려면 실제로 A 레코드와 CNAME 레코드를 둘 다 만들어야 합니다. A 레코드는 DNS 확인 자체를 위한 것이고 CNAME 레코드는 Azure에서 사용자가 사용자 지정 도메인 이름을 소유했는지 확인하기 위한 것입니다.

사용자의 A 레코드는 다음과 같이 구성되어야 합니다(@는 일반적으로 루트 도메인으로 표시됨).
 
<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 예</th>
    <th>호스트</th>
    <th>값</th>
  </tr>
  <tr>
    <td>contoso.com(루트)</td>
    <td>@</td>
    <td><a href="#vip">1단계</a>의 IP 주소</td>
  </tr>
  <tr>
    <td>www.contoso.com(하위)</td>
    <td>www</td>
    <td><a href="#vip">1단계</a>의 IP 주소</td>
  </tr>
  <tr>
    <td>*.contoso.com(와일드 카드)</td>
    <td>*</td>
    <td><a href="#vip">1단계</a>의 IP 주소</td>
  </tr>
</table>

추가 CNAME 레코드는 awverify.&lt;*subdomain*>.&lt;*rootdomain*>에서 awverify.&lt;*subdomain*>.azurewebsites.net으로 매핑하는 규칙을 사용합니다. 아래 예제를 참조하세요.

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 예</th>
    <th>CNAME 호스트</th>
    <th>CNAME 값</th>
  </tr>
  <tr>
    <td>contoso.com(루트)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com(하위)</td>
    <td>awverify.www</td>
    <td>awverify.www.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com(와일드 카드)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### CNAME 레코드 만들기

Azure 앱의 기본 도메인 이름에 매핑하는 데 CNAME 레코드를 사용하는 경우 A 레코드로 수행하는 것처럼 추가 CNAME 레코드가 필요하지 않습니다.

>[AZURE.IMPORTANT] 루트 도메인에 대한 CNAME 레코드를 만들지 마세요(즉, "루트 레코드"). 자세한 내용은 [루트 도메인에서 CNAME 레코드를 사용할 수 없는 이유](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)를 참조하세요. 루트 도메인을 Azure 앱에 매핑하려면 대신 [A 레코드](#a)를 사용하세요.

사용자의 CNAME 레코드는 다음과 같이 구성되어야 합니다(@는 일반적으로 루트 도메인으로 표시됨).

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN 예</th>
    <th>CNAME 호스트</th>
    <th>CNAME 값</th>
  </tr>
  <tr>
    <td>www.contoso.com(하위)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com(와일드 카드)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
## 3단계. 앱에 대해 사용자 지정 도메인 이름 사용

Azure 포털의 **외부 도메인 가져오기** 블레이드로 돌아가([1단계](#vip) 참조) 사용자 지정 도메인의 정규화된 도메인 이름(FQDN)을 목록에 추가해야 합니다.

1.	로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2.	Azure 포털의 왼쪽 메뉴에서 **앱 서비스**를 클릭합니다.

4.	앱을 클릭한 후 **설정** > **사용자 지정 도메인 및 SSL** > **외부 도메인 가져오기**를 클릭합니다.

2.	사용자 지정 도메인의 FQDN을 목록에 추가합니다(예: **www.contoso.com**).

    ![Azure 앱에 사용자 지정 도메인 이름 매핑: 도메인 이름 목록에 추가](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure에서는 여기서 사용할 도메인 이름을 확인하려고 시도하므로 [2단계](#createdns)에서 DNS 레코드를 만들었던 동일한 도메인 이름인지 확인해야 합니다. 맞는 경우

6.  **Save**를 클릭합니다.

7.  새 사용자 지정 도메인 이름이 성공적으로 구성되면 브라우저에서 사용자 지정 도메인 이름으로 이동합니다. 이제 실행 중인 앱과 사용자 지정 도메인 이름이 표시됩니다.

<a name="verify"></a>
## DNS 적용 확인

구성 단계를 완료하고 나면 DNS 공급자에 따라 변경 내용이 적용되는 데 다소 시간이 걸릴 수 있습니다. [http://digwebinterface.com/](http://digwebinterface.com/)을 사용하여 예상대로 DNS 적용이 작동하는지 확인할 수 있습니다. 사이트를 찾은 후 텍스트 상자에 호스트 이름을 지정하고 **Dig**를 클릭합니다. 최근 변경 내용이 적용되었는지 확인하려면 결과를 확인합니다.

![Azure 앱에 사용자 지정 도메인 이름 매핑: DNS 전파 확인](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] DNS 항목이 적용되는 데는 최대 48시간이 걸릴 수 있습니다(더 걸리는 경우도 있음). 모든 항목이 올바르게 구성된 경우 성공적으로 적용될 때까지 기다려야 합니다.

## 다음 단계

[Azure DNS 시작](../dns/dns-getstarted-create-dnszone.md) [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](../dns/dns-web-sites-custom-domain.md) [Azure DNS로 도메인 위임](../dns/dns-domain-delegation.md)

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0803_2016-->