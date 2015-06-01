#Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성

웹 사이트를 만들 때 Azure에서 azurewebsites.net 도메인에 친숙한 하위 도메인을 제공하므로, 사용자는 http://&lt;mysite>.azurewebsites.net과 같은 URL을 사용하여 웹 사이트에 액세스할 수 있습니다. 그러나 공유 또는 표준 모드에 대해 웹 사이트를 구성하는 경우 웹 사이트를 고유한 도메인 이름에 매핑할 수 있습니다.

선택적으로, Azure 트래픽 관리자를 사용하여 웹 사이트로 들어오는 트래픽의 부하를 분산할 수도 있습니다. 웹 사이트에서 트래픽 관리자를 사용하는 방법에 대한 자세한 내용은 [Azure 트래픽 관리자를 사용하여 Azure 웹 사이트 트래픽 제어][trafficmanager]를 참조하세요.

> [AZURE.NOTE]이 작업의 절차는 Azure 웹 사이트에 적용됩니다. 클라우드 서비스의 경우 <a href="/develop/net/common-tasks/custom-dns/">Azure에서 사용자 지정 도메인 이름 구성</a>을 참조하세요.

> [AZURE.NOTE]이 작업의 단계를 수행하려면 고유 또는 표준 모드에 대해 웹 사이트를 구성해야 하며, 구독에 대해 청구되는 요금이 변경될 수도 있습니다. 자세한 내용은 <a href="/pricing/details/web-sites/">웹 사이트 가격 정보</a>를 참조하세요.

이 문서의 내용

-   [CNAME 및 A 레코드 이해](#understanding-records)
-   [공유 또는 표준 모드에 대해 웹 사이트 구성](#bkmk_configsharedmode)
-   [트래픽 관리자에 웹 사이트 추가](#trafficmanager)
-   [사용자 지정 도메인에 대한 CNAME 추가](#bkmk_configurecname)
-   [사용자 지정 도메인에 대한 A 레코드 추가](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>CNAME 및 A 레코드 이해</h2>

CNAME(또는 별칭 레코드) 및 A 레코드는 둘 다 도메인 이름을 웹 사이트에 연결할 수 있게 해 주지만 각각 다르게 작동합니다.

###CNAME 또는 별칭 레코드

CNAME 레코드는 *contoso.com*, **www.contoso.com** 등의 **특정** 도메인을 정식 도메인 이름에 매핑합니다. 이 경우 정식 도메인 이름은 Azure 웹 사이트의 **&lt;myapp>.azurewebsites.net** 도메인 이름 또는 트래픽 관리자 프로필의 **&lt;myapp>.trafficmgr.com** 도메인 이름입니다. CNAME을 만들면 **&lt;myapp>.azurewebsites.net** 또는 **&lt;myapp>.trafficmgr.com** 도메인 이름에 대한 별칭이 만들어집니다. CNAME 항목은 **&lt;myapp>.azurewebsites.net** 또는 **&lt;myapp>.trafficmgr.com** 도메인 이름의 IP 주소로 자동으로 확인되므로 웹 사이트의 IP 주소가 변경될 경우 특별한 조치를 수행할 필요가 없습니다.

> [AZURE.NOTE]www.contoso.com과 같은 CNAME 레코드를 사용하고 contoso.com과 같은 비루트 이름을 사용하면 일부 도메인 등록 기관에서 하위 도메인만 매핑할 수 있습니다. CNAME 레코드에 대한 자세한 내용은 등록 기관에서 제공하는 설명서인 <a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME 레코드에 대한 Wikipedia 항목</a> 또는 <a href="http://tools.ietf.org/html/rfc1035">IETF 도메인 이름 - 구현 및 사양</a> 문서를 참조하세요.

###A 레코드

A 레코드는 **contoso.com**, **www.contoso.com** 등의 도메인이나 **.contoso.com* 등의 **와일드카드 도메인**을 IP 주소에 매핑합니다. Azure 웹 사이트의 경우 서비스의 가상 IP 또는 웹 사이트용으로 구입한 특정 IP 주소입니다. 따라서 A 레코드가 CNAME 레코드보다 나은 주요 장점은 ***.contoso.com**과 같이 와일드카드를 사용하는 항목을 사용할 수 있다는 것입니다. 이러한 항목은 **mail.contoso.com**, **login.contoso.com**, **www.contso.com** 등의 여러 하위 도메인에 대한 요청을 처리합니다.

> [AZURE.NOTE]A 레코드는 고정 IP 주소에 매핑되므로 변경 내용을 웹 사이트의 IP 주소로 자동으로 확인할 수 없습니다. A 레코드에 사용할 IP 주소는 웹 사이트에 대한 사용자 지정 도메인 이름 설정을 구성할 때 제공됩니다. 그러나 웹 사이트를 삭제하고 다시 만들거나 웹 사이트 모드를 다시 무료로 변경할 경우 이 값이 변경될 수도 있습니다.

> [AZURE.NOTE]A 레코드는 트래픽 관리자를 사용한 부하 분산에 사용할 수 없습니다. 자세한 내용은 [Azure 트래픽 관리자를 사용하여 Azure 웹 사이트 트래픽 제어][trafficmanager]를 참조하십시오.
 
<a name="bkmk_configsharedmode"></a><h2>공유 또는 표준 모드에 대해 웹 사이트 구성</h2>

Azure 웹 사이트의 공유 및 표준 모드에서만 웹 사이트의 사용자 지정 도메인 이름을 설정할 수 있습니다. 웹 사이트를 무료 웹 사이트 모드에서 공유 또는 표준 웹 사이트 모드로 전환하기 전에 먼저 웹 사이트 구독에 대해 적용된 지출 한도를 제거해야 합니다. 공유 및 표준 모드 가격 책정에 대한 자세한 내용은 [가격 책정 세부 정보][PricingDetails]를 참조하십시오.

1. 브라우저에서 [관리 포털][portal]을 엽니다.
2. **웹 사이트** 탭에서 사이트 이름을 클릭합니다.

	![][standardmode1]

3. **SCALE** 탭을 클릭합니다.

	![][standardmode2]

	
4. **일반** 섹션에서 **공유**를 클릭하여 웹 사이트 모드를 설정합니다.

	![][standardmode3]

	> [AZURE.NOTE]이 웹 사이트에서 트래픽 관리자를 사용하려면 공유 대신 표준 코드를 선택해야 합니다.

5. **Save**를 클릭합니다.
6. 공유 모드(또는 표준을 선택한 경우 표준 모드) 시 비용 증가에 대한 메시지가 표시될 때 동의하면 **Yes**를 클릭합니다.

	<!--![][standardmode4]-->

	**참고**<br /> “‘웹 사이트 이름’ 웹 사이트에 대한 규모 구성 실패” 오류가 표시되는 경우 자세히 단추를 사용하여 추가 정보를 확인할 수 있습니다.

<a name="trafficmanager"></a><h2>(옵션) 트래픽 관리자에 웹 사이트 추가</h2>

트래픽 관리자에서 웹 사이트를 사용하려면 다음 단계를 수행하세요.

1. 트래픽 관리자 프로필이 아직 없는 경우 [빠른 생성을 사용하여 트래픽 관리자 프로필 만들기][createprofile]의 내용을 참조하여 만듭니다. 트래픽 관리자 프로필과 관련된 **.trafficmgr.com** 도메인 이름이 있습니다. 이 이름은 이후 단계에서 사용됩니다.

2. [끝점 추가 또는 삭제][addendpoint]의 내용을 참조하여 트래픽 관리자 프로필에서 웹 사이트를 끝점으로 추가합니다.

	> [AZURE.NOTE]끝점을 추가할 때 웹 사이트가 목록에 나열되지 않는 경우 웹 사이트가 표준 모드로 구성되었는지 확인합니다. 표준 모드로 구성된 웹 사이트만 트래픽 관리자에서 작동합니다.

3. DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.

4. 이제 CNAME 레코드를 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다. **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.

5. 또한 CNAME에 대한 도메인 또는 하위 도메인 별칭을 제공해야 합니다. 예를 들어 **www.customdomain.com**에 대한 별칭을 만들려는 경우 **www**입니다.

5. 또한 이 CNAME 별칭에 대한 정식 도메인 이름인 호스트 이름을 제공해야 합니다. 이는 웹 사이트의 **.trafficmgr.com** 이름입니다.

예를 들어 다음 CNAME 레코드는 **www.contoso.com**의 모든 트래픽을 웹 사이트의 도메인 이름인 **contoso.trafficmgr.com**으로 전달합니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>별칭/호스트 이름/하위 도메인</strong></td>
<td><strong>정식 도메인</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

**www.contoso.com**의 방문자에게는 실제 호스트(contoso.azurewebsite.net)가 표시되지 않으므로 최종 사용자가 전달 프로세스를 볼 수 없습니다.

> [AZURE.NOTE]웹 사이트에 트래픽 관리자를 사용하는 경우 '**사용자 지정 도메인에 대한 CNAME 추가**' 및 '**사용자 지정 도메인에 대한 A 레코드 추가**' 섹션의 단계를 따를 필요가 없습니다. 이전 단계에서 만들어진 CNAME 레코드는 들어오는 트래픽을 트래픽 관리자로 라우팅하고, 트래픽 관리자는 웹 사이트 끝점으로 트래픽을 라우팅합니다.

<a name="bkmk_configurecname"></a><h2>사용자 지정 도메인에 대한 CNAME 추가</h2>

CNAME 레코드를 만들려면 등록 기관에서 제공한 도구를 사용하여 사용자 지정 도메인에 대한 새 항목을 DNS 테이블에 추가해야 합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다.

1. 이러한 방법 중 하나를 사용하여 웹 사이트에 할당된 **.azurewebsite.net** 도메인 이름을 찾습니다.

	* [Azure 관리 포털][portal]에 로그인하고 웹 사이트를 선택한 다음 **대시보드**를 선택하고 **간략 상태** 섹션에서 **사이트 URL** 항목을 찾습니다.

	* [Azure Powershell](/manage/install-and-configure-windows-powershell/)을 설치 및 구성하고 다음 명령을 사용합니다.

			get-azurewebsite yoursitename | select hostnames

	* [Azure 플랫폼 간 명령줄 인터페이스](/manage/install-and-configure-cli/)를 설치 및 구성하고 다음 명령을 사용합니다.

			azure site domain list yoursitename

	다음 단계에서 사용되므로 이 **.azurewebsite.net** 이름을 저장합니다.

3. DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.

4. 이제 CNAME 레코드를 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다. **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.

5. 또한 CNAME에 대한 도메인 또는 하위 도메인 별칭을 제공해야 합니다. 예를 들어 **www.customdomain.com**에 대한 별칭을 만들려는 경우 **www**입니다. 루트 도메인에 대한 별칭을 만들려는 경우 등록 기관의 DNS 도구에서 '**@**' 기호로 표시될 수도 있습니다.

5. 또한 이 CNAME 별칭에 대한 정식 도메인 이름인 호스트 이름을 제공해야 합니다. 이는 웹 사이트의 **.azurewebsite.net** 이름입니다.

예를 들어 다음 CNAME 레코드는 **www.contoso.com**의 모든 트래픽을 웹 사이트의 도메인 이름인 **contoso.azurewebsite.net**으로 전달합니다.

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>별칭/호스트 이름/하위 도메인</strong></td>
<td><strong>정식 도메인</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

**www.contoso.com**의 방문자에게는 실제 호스트(contoso.azurewebsite.net)가 표시되지 않으므로 최종 사용자가 전달 프로세스를 볼 수 없습니다.

> [AZURE.NOTE]위 예제는 __www__ 하위 도메인의 트래픽에만 적용됩니다. CNAME 레코드와 함께 와일드카드를 사용할 수 없으므로 각 도메인/하위 도메인에 대해 하나의 CNAME을 만들어야 합니다. *.contoso.com 등의 하위 도메인에서 azurewebsite.net 주소로 트래픽을 보내려는 경우 DNS 설정에서 __URL 리디렉션__ 또는 __URL 전달__ 항목을 구성하거나 A 레코드를 만들 수 있습니다.

> [AZURE.NOTE]CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. CNAME이 전파될 때까지 웹 사이트에 대한 CNAME을 설정할 수 없습니다. <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.

###웹 사이트에 도메인 이름 추가

도메인 이름에 대한 CNAME 레코드가 전파된 후 웹 사이트와 연결해야 합니다. Azure 플랫폼 간 명령줄 인터페이스나 Azure 관리 포털을 사용하여 CNAME 레코드에서 정의된 사용자 지정 도메인 이름을 웹 사이트에 추가할 수 있습니다.

**명령줄 도구를 사용하여 도메인 이름을 추가하려면**

[Azure 플랫폼 간 명령줄 인터페이스](/manage/install-and-configure-cli/)를 설치 및 구성하고 다음 명령을 사용합니다.

	azure site domain add customdomain yoursitename

예를 들어 다음은 **www.contoso.com**의 사용자 지정 도메인 이름을 **contoso.azurewebsite.net** 웹 사이트에 추가합니다.

	azure site domain add www.contoso.com contoso

다음 명령을 사용하여 사용자 지정 도메인 이름이 웹 사이트에 추가되었는지 확인할 수 있습니다.

	azure site domain list yoursitename

이 명령에서 반환된 목록에는 사용자 지정 도메인 이름과 기본 **.azurewebsite.net** 항목이 포함되어야 합니다.

**Azure 관리 포털을 사용하여 도메인 이름을 추가하려면**

1. 브라우저에서 [Azure 관리 포털][portal]을 엽니다.

2. **웹 사이트** 탭에서 사이트 이름을 클릭하고 **대시보드**를 선택한 후 페이지 맨 아래에서 **도메인 관리**를 선택합니다.

	![][setcname2]

6. **DOMAIN NAMES** 텍스트 상자에 구성한 도메인 이름을 입력합니다.

	![][setcname3]

6. 확인 표시를 클릭하여 도메인 이름을 적용합니다.

구성이 완료되면 사용자 지정 도메인 이름이 웹 사이트 **구성** 페이지의 **도메인 이름** 섹션에 표시됩니다.

<a name="bkmk_configurearecord"></a><h2>사용자 지정 도메인에 대한 A 레코드 추가</h2>

A 레코드를 만들려면 먼저 웹 사이트의 IP 주소를 찾아야 합니다. 그런 다음 등록 기관에서 제공한 도구를 사용하여 사용자 지정 도메인에 대한 항목을 DNS 테이블에 추가합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 A 레코드를 지정하지만 개념은 동일합니다. A 레코드를 만드는 것 외에도 Azure에서 A 레코드를 확인하는 데 사용하는 CNAME 레코드를 만들어야 합니다.

1. 브라우저에서 [Azure 관리 포털][portal]을 엽니다.

2. **웹 사이트** 탭에서 사이트 이름을 클릭하고 **대시보드**를 선택한 후 화면 맨 아래에서 **도메인 관리**를 선택합니다.

	![][setcname2]

5. **Manage custom domains** 대화 상자에서 **The IP Address to use when configuring A records**를 찾습니다. IP 주소를 복사합니다. 이 IP 주소는 A 레코드를 만들 때 사용됩니다.

5. **Manage custom domains** 대화 상자 맨 위의 텍스트 끝에 있는 awverify 도메인 이름을 확인합니다. 이름이 **awverify.mysite.azurewebsites.net**이어야 합니다. 여기서 **mysite**는 해당 웹 사이트의 이름입니다. 이 이름은 검증 CNAME 레코드를 만들 때 사용되는 도메인 이름이므로 복사합니다.

6. DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.

6. A 및 CNAME 레코드를 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다.

7. 다음 단계에 따라 A 레코드를 만듭니다.

	1. A 레코드를 사용할 도메인 또는 하위 도메인을 선택하거나 입력합니다. 예를 들어 **www.customdomain.com**에 대한 별칭을 만들려는 경우 **www**를 선택합니다. 모든 하위 도메인에 대한 와일드카드 항목을 만들려면 '__*__'를 입력합니다. 그러면 **mail.customdomain.com**, **login.customdomain.com**, **www.customdomain.com** 등의 모든 하위 도메인이 포함됩니다.

		루트 도메인에 대한 A 레코드를 만들려는 경우 등록 기관의 DNS 도구에서 '@' 기호로 표시될 수도 있습니다.

	2. 제공된 필드에 클라우드 서비스의 IP 주소를 입력합니다. 그러면 A 레코드에 사용된 도메인 항목이 클라우드 서비스 배포의 IP 주소와 연결됩니다.

		예를 들어 다음 A 레코드는 모든 트래픽을 contoso.com에서 배포된 응용 프로그램의 IP 주소인 137.135.70.239로 전달합니다.

		<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tr>
		<td><strong>호스트 이름/하위 도메인</strong></td>
		<td><strong>IP address</strong></td>
		</tr>
		<tr>
		<td>@</td>
		<td>137.135.70.239</td>
		</tr>
		</table>

		이 예제에서는 루트 도메인에 대한 A 레코드를 만드는 방법을 보여 줍니다. 모든 하위 도메인을 포함할 와일드카드 항목을 만들려면 '__*__'를 하위 도메인으로 입력합니다.

7. 별칭이 **awverify**이고 정식 도메인이 앞에서 얻은 **awverify.mysite.azurewebsites.net**인 CNAME 레코드를 만듭니다.

	> [AZURE.NOTE]일부 등록 기관에서는 별칭 awverify를 허용할 수도 있지만 다른 등록 기관에서는 전체 별칭 도메인 이름인 awverify.www.customdomainname.com 또는 awverify.customdomainname.com을 요구할 수 있습니다.

	예를 들어 다음은 Azure에서 A 레코드 구성을 확인하는 데 사용할 수 있는 CNAME 레코드를 만듭니다.

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>별칭/호스트 이름/하위 도메인</strong></td>
<td><strong>정식 도메인</strong></td>
</tr>
<tr>
<td>awverify</td>
<td>awverify.contoso.azurewebsites.net</td>
</tr>
</table>

> [AZURE.NOTE]awverify CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. awverify CNAME이 전파될 때가지 A 레코드에서 웹 사이트에 대해 정의한 사용자 지정 도메인 이름을 설정할 수 없습니다. <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.

###웹 사이트에 도메인 이름 추가

도메인 이름에 대한 **awverify** CNAME 레코드가 전파된 후 A 레코드에서 정의된 사용자 지정 도메인을 해당 웹 사이트와 연결할 수 있습니다. Azure 플랫폼 간 명령줄 인터페이스나 Azure 관리 포털을 사용하여 A 레코드에서 정의된 사용자 지정 도메인 이름을 웹 사이트에 추가할 수 있습니다.

**명령줄 도구를 사용하여 도메인 이름을 추가하려면**

[Azure 플랫폼 간 명령줄 인터페이스](/manage/install-and-configure-cli/)를 설치 및 구성하고 다음 명령을 사용합니다.

	azure site domain add customdomain yoursitename

예를 들어 다음은 **contoso.com**의 사용자 지정 도메인 이름을 **contoso.azurewebsite.net** 웹 사이트에 추가합니다.

	azure site domain add contoso.com contoso

다음 명령을 사용하여 사용자 지정 도메인 이름이 웹 사이트에 추가되었는지 확인할 수 있습니다.

	azure site domain list yoursitename

이 명령에서 반환된 목록에는 사용자 지정 도메인 이름과 기본 **.azurewebsite.net** 항목이 포함되어야 합니다.

**Azure 관리 포털을 사용하여 도메인 이름을 추가하려면**

1. 브라우저에서 [Azure 관리 포털][portal]을 엽니다.

2. **웹 사이트** 탭에서 사이트 이름을 클릭하고 **대시보드**를 선택한 후 페이지 맨 아래에서 **도메인 관리**를 선택합니다.

	![][setcname2]

6. **DOMAIN NAMES** 텍스트 상자에 구성한 도메인 이름을 입력합니다.

	![][setcname3]

6. 확인 표시를 클릭하여 도메인 이름을 적용합니다.

구성이 완료되면 사용자 지정 도메인 이름이 웹 사이트 **구성** 페이지의 **도메인 이름** 섹션에 표시됩니다.

> [AZURE.NOTE]A 레코드에서 정의된 사용자 지정 도메인 이름을 해당 웹 사이트에 추가한 후 등록 기관에서 제공한 도구를 사용하여 awverify CNAME 레코드를 제거할 수 있습니다. 그러나 나중에 다른 A 레코드를 추가하려면 먼저 awverify 레코드를 다시 만들어야 새 A 레코드에서 정의된 새 도메인 이름을 웹 사이트와 연결할 수 있습니다.

##다음 단계

-   [웹 사이트를 관리하는 방법](/manage/services/web-sites/how-to-manage-websites/)

-   [웹 사이트에 대한 SSL 인증서 구성](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]: #bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/web-sites-traffic-manager.md
[addendpoint]: http://msdn.microsoft.com/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

<!--HONumber=52-->
