
# Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성

Azure에서 응용 프로그램을 만드는 경우 Azure에서 cloudapp.net 도메인의 하위 도메인을 제공하므로 사용자가 http://<*myapp*>.cloudapp.net 등의 URL에서 응용 프로그램에 액세스할 수 있습니다. 그러나 contoso.com 등의 고유한 도메인 이름에 응용 프로그램을 표시할 수도 있습니다.

> [WACOM.NOTE] 
이 작업의 절차는 Azure 클라우드 서비스에 적용됩니다. 저장소 계정의 경우 [Azure 저장소 계정에 대한 사용자 지정 도메인 이름 구성](../storage-custom-domain-name/)을 참조하십시오. 웹 사이트의 경우 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](../web-sites-custom-domain-name/)을 참조하십시오.

이 문서에서는 다음을 수행합니다.

* [CNAME 및 A 레코드 이해](#access-app)
* [사용자 지정 도메인에 대한 CNAME 레코드 추가](#add-cname)
* [사용자 지정 도메인에 대한 A 레코드 추가](#add-aname)

<h2><a name="access-app"></a>CNAME 및 A 레코드 이해</h2>


CNAME(또는 별칭 레코드) 및 A 레코드는 둘 다 도메인 이름을 특정 서버(또는 이 경우 서비스)에 연결할 수 있게 해 주지만 다르게 작동합니다. Azure 클라우드 서비스와 함께 A 레코드를 사용하는 경우의 몇 가지 특정 고려 사항도 있으며, 사용할 레코드를 결정하기 전에 고려해야 합니다.

### CNAME 또는 별칭 레코드

CNAME 레코드는 **contoso.com**, **www.contoso.com** 등의 *특정* 도메인을 정식 도메인 이름에 매핑합니다. 이 경우 정식 도메인 이름은 Azure 호스티드 응용 프로그램의 **<myapp>.cloudapp.net** 도메인 이름입니다. CNAME을 만들면 **<myapp>.cloudapp.net**에 대한 별칭이 만들어집니다. CNAME 항목은 **<myapp>.cloudapp.net** 서비스의 IP 주소로 자동으로 확인되므로 클라우드 서비스의 IP 주소가 변경될 경우 특별한 조치를 수행할 필요가 없습니다.

> [WACOM.NOTE] 
일부 도메인 등록 기관에서는 contoso.com 등의 루트 이름이 아니라 www.contoso.com 등의 CNAME 레코드를 사용할 때 하이 도메인 매핑만 허용합니다. CNAME 레코드에 대한 자세한 내용은 등록 기관에서 제공한 설명서, [CNAME 레코드에 대한 Wikipedia 항목][1] 또는 [IETF 도메인 이름 - 구현 및 사양][2] 문서를 참조하십시오.

### A 레코드

A 레코드는 **contoso.com**, **www.contoso.com** 등의 도메인이나 **\*.contoso.com** 등의 *와일드카드 도메인*을 IP 주소에 매핑합니다. Azure 클라우드 서비스의 경우 서비스의 가상 IP입니다. 따라서 A 레코드가 CNAME 레코드보다 나은 주요 장점은 **\*.contoso.com**과 같이 와일드카드를 사용하는 항목을 사용할 수 있다는 것입니다. 이러한 항목은 **mail.contoso.com**, **login.contoso.com**, **www.contso.com** 등의 여러 하위 도메인에 대한 요청을 처리합니다.

> [WACOM.NOTE] 
A 레코드는 고정 IP 주소에 매핑되므로 변경 내용을 클라우드 서비스의 IP 주소로 자동으로 확인할 수 없습니다. 빈 슬롯(프로덕션 또는 스테이징)에 처음 배포할 때 클라우드 서비스에서 사용되는 IP 주소가 할당됩니다. 슬롯에 대한 배포를 삭제하면 Azure에서 IP 주소를 해제하며, 나중에 슬롯에 배포할 때 새 IP 주소가 지정될 수 있습니다.
> 
> 편의상, 스테이징 배포와 프로덕션 배포 간에 전환하거나 기존 배포의 바로 업그레이드를 수행하는 경우 주어진 배포 슬롯(프로덕션 또는 스테이징)의 IP 주소가 지속됩니다. 이러한 작업을 수행하는 방법에 대한 자세한 내용은 [클라우드 서비스를 관리하는 방법](../cloud-services-how-to-manage/)을 참조하십시오.

<h2><a name="add-cname"></a>사용자 지정 도메인에 대한 CNAME 레코드 추가</h2>


CNAME 레코드를 만들려면 등록 기관에서 제공한 도구를 사용하여 사용자 지정 도메인에 대한 새 항목을 DNS 테이블에 추가해야
합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다.

1. 이러한 방법 중 하나를 사용하여 클라우드 서비스에 할당된 **.cloudapp.net** 도메인 이름을 찾습니다.

	* [Azure 관리 포털][3]에 로그인하고 클라우드 서비스를 선택한 다음 **대시보드**를 선택하고 **quick glance** 섹션에서 **Site URL** 항목을 찾습니다.

 			![사이트 URL을 표시하는 한눈에 보기 섹션](./media/custom-dns/csurl.png) 
	* [Azure Powershell](../install-configure-powershell/)을 설치 및 구성하고 다음 명령을 사용합니다.
  
  		Get-AzureDeployment -ServiceName yourservicename \| Select Url

	CNAME 레코드를 만들 때 필요하므로 두 방법 중 하나에서 반환된 URL에 사용된 도메인 이름을 저장합니다.

1.  DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.

2.  이제 CNAME을 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다. **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.

3.  **www.customdomain.com**에 대한 별칭을 만들려는 경우 **www**와 같은 CNAME에 대한 도메인 또는 하위 도메인 별칭도 제공해야 합니다. 루트 도메인에 대한 별칭을 만들려는 경우 등록 기관의 DNS 도구에서 \'**@**\' 기호로 표시될 수도 있습니다.

4.  정식 호스트 이름을 제공해야 합니다. 이 경우에는 응용 프로그램의 **cloudapp.net** 도메인입니다.

예를 들어 다음 CNAME 레코드는 **www.contoso.com**의 모든 트래픽을 배포된 응용 프로그램의 사용자 지정 도메인 이름인 **contoso.cloudapp.net**으로 전달합니다.

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>별칭/호스트 이름/하위 도메인</strong>
</td>

<td><strong>정식 도메인</strong>
</td>

</tr>

<tr>
<td>www</td>

<td>contoso.cloudapp.net</td>

</tr>

</table>

**www.contoso.com**의 방문자에게는 실제 호스트(contoso.cloudapp.net)가 표시되지 않으므로 최종 사용자가 전달 프로세스를 볼 수 없습니다.

> [WACOM.NOTE] 
위 예제는 **www** 하위 도메인의 트래픽에만 적용됩니다. CNAME 레코드와 함께 와일드카드를 사용할 수 없으므로 각 도메인/하위 도메인에 대해 하나의 CNAME을 만들어야 합니다. \*.contoso.com 등의 하위 도메인에서 cloudapp.net 주소로 트래픽을 보내려는 경우 DNS
설정에서 **URL 리디렉션** 또는 **URL 전달** 항목을 구성하거나 A 레코드를 만들 수 있습니다.

<h2><a name="add-aname"></a>사용자 지정 도메인에 대한 A 레코드 추가</h2>


A 레코드를 만들려면 먼저 클라우드 서비스의 가상 IP 주소를 찾아야 합니다. 그런 다음 등록 기관에서 제공한 도구를 사용하여
사용자 지정 도메인에 대한 새 항목을 DNS 테이블에 추가합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 A 레코드를
지정하지만 개념은 동일합니다.

1.  다음 방법 중 하나를 사용하여 클라우드 서비스의 IP 주소를 가져옵니다.
	* [Azure 관리 포털][3]에 로그인하고 클라우드 서비스를 선택한 다음 **대시보드**를 선택하고 **quick glance** 섹션에서 **Public Virtual IP (VIP) address** 항목을 찾습니다.

 		![VIP를 표시하는 한눈에 보기 섹션](./media/custom-dns/csvip.png) 
	* [Azure Powershell](../install-configure-powershell/)을 설치 및 구성하고 다음 명령을
  		사용합니다.
  
  get-azurevm -servicename yourservicename \| get-azureendpoint -VM \{$\_.VM} \| select Vip
  
  여러 끝점이 클라우드 서비스에 연결되어 있는 경우 IP 주소가 포함된 여러 줄을 받지만 모두 동일한 주소가 표시되어야 합니다.

	A 레코드를 만들 때 필요하므로 IP 주소를 저장합니다.

1.  DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.

2.  이제 A 레코드를 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다.

3.  이 A 레코드를 사용할 도메인 또는 하위 도메인을 선택하거나 입력합니다. 예를 들어 **www.customdomain.com**에 대한 별칭을 만들려는 경우 **www**를 선택합니다. 모든 하위 도메인에 대한 와일드카드 항목을 만들려면 \'**\***\'를 입력합니다. 그러면 **mail.customdomain.com**, **login.customdomain.com**, **www.customdomain.com** 등의 모든 하위 도메인이 포함됩니다.

루트 도메인에 대한 A 레코드를 만들려는 경우 등록 기관의 DNS 도구에서 \'**@**\' 기호로 표시될 수도 있습니다.

1.  제공된 필드에 클라우드 서비스의 IP 주소를 입력합니다. 그러면 A 레코드에 사용된 도메인 항목이 클라우드 서비스 배포의
    IP 주소와 연결됩니다.

예를 들어 다음 A 레코드는 모든 트래픽을 **contoso.com**에서 배포된 응용 프로그램의 IP 주소인 **137.135.70.239**로 전달합니다.

<table  border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>호스트 이름/하위 도메인</strong>
</td>

<td><strong>IP 주소</strong>
</td>

</tr>

<tr>
<td>@</td>

<td>137.135.70.239</td>

</tr>

</table>

이 예제에서는 루트 도메인에 대한 A 레코드를 만드는 방법을 보여 줍니다. 모든 하위 도메인을 포함할 와일드카드 항목을 만들려면
\'**\***\'를 하위 도메인으로 입력합니다.

## 다음 단계

* [클라우드 서비스를 관리하는 방법](../cloud-services-how-to-manage/)
* [CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법][4]



[1]: http://en.wikipedia.org/wiki/CNAME_record
[2]: http://tools.ietf.org/html/rfc1035
[3]: https://manage.windowsazure.com
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx