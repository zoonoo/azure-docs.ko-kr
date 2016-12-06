## <a name="what-is-reverse-dns"></a>역방향 DNS란?

기본 DNS 레코드를 통해 DNS 이름(예: 'www.contoso.com')을 IP 주소(예: 64.4.6.100)에 매핑할 수 있습니다.  역방향 DNS로는 IP 주소(64.4.6.100)를 다시 이름('www.contoso.com')으로 변환할 수 있습니다.

역방향 DNS 레코드는 다양한 상황에 사용됩니다. 예를 들어 역방향 DNS 레코드는 보낸 사람의 메일 메시지를 확인하여 스팸 메일을 방지하는 데 널리 사용됩니다.  수신 메일 서버는 보내는 서버의 IP 주소의 역방향 DNS 레코드를 검색하고, 해당 호스트가 원래의 도메인에서 메일을 보내도록 허가를 받았는지 확인합니다. 하지만 [Azure Compute Services는 외부 도메인으로의 전자 메일 전송을 지원하지 않습니다.](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="how-reverse-dns-works"></a>역방향 DNS 작동 방식

역방향 DNS 레코드는 'ARPA' 영역이라는 특수한 DNS 영역에서 호스팅됩니다.  이러한 영역은 'contoso.com'과 같은 일반적인 계층 구조 호스팅 도메인과 병렬로 별도의 DNS 계층 구조를 형성합니다.

예를 들어 DNS 레코드 'www.contoso.com'은 영역 'contoso.com'에 이름 'www'를 포함하는 DNS 'A' 레코드를 사용하여 구현됩니다.  이 A 레코드는 해당 IP 주소(이 경우 64.4.6.100)를 가리킵니다.  역방향 조회는 영역 '6.4.64.in-addr.arpa'에서 '100'으로 명명된 'PTR' 레코드를 사용하여 별도로 구현됩니다(해당 IP 주소는 ARPA 영역에서 반전됨).  이 PTR 레코드가 올바르게 구성된 경우 이름 'www.contoso.com'을 가리킵니다.

조직에 IP 주소 블록이 할당되면 해당 ARPA 영역을 관리할 권한도 획득하게 됩니다. Azure에 사용되는 IP 주소 블록에 해당되는 ARPA 영역은 Microsoft에서 호스트 및 관리합니다. ISP는 사용자 고유의 IP 주소에 대한 ARPA 영역을 호스트하거나 사용자가 Azure DNS와 같은 선택한 DNS 서비스에서 ARPA 영역을 호스트하도록 할 수 있습니다.

> [!NOTE]
> 정방향 및 역방향 DNS 조회를 DNS 계층과 함께 별도로 구현합니다. 'www.contoso.com'에 대한 역방향 조회는 영역 'contoso.com'에서 호스트되지 **않으며** 대신 해당 IP 주소 블록에 대한 ARPA 영역에서 호스트됩니다.

역방향 DNS에 대한 자세한 내용은 [역방향 DNS 조회](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)를 참조하세요.

## <a name="azure-support-for-reverse-dns"></a>역방향 DNS에 대한 Azure 지원

Azure에서는 역방향 DNS와 관련하여 두 가지 별도의 시나리오를 지원합니다.

1. IP 주소 블록에 해당하는 ARPA 영역 호스팅
2. Azure 서비스에 할당된 IP 주소에 대한 역방향 DNS를 구성할 수 있습니다.

전자를 지원하기 위해 Azure DNS를 사용하여 ARPA 영역을 호스트하고 각 역방향 DNS 조회를 위해 PTR 레코드를 관리할 수 있습니다.  ARPA 영역을 만들고 위임을 설정하며 PTR 레코드를 구성하는 과정은 일반 DNS 영역과 같습니다.  유일한 차이점은 DNS 등록 기관보다는 ISP를 통해 위임을 구성해야 하며 PTR 레코드 유형만 사용해야 한다는 점입니다.

후자를 지원하기 위해 Azure에서는 서비스에 할당된 IP 주소에 대한 역방향 조회를 구성할 수 있도록 합니다.  이 역방향 조회는 Azure에 의해 해당 ARPA 영역에서 PTR 레코드로 구성됩니다.  Azure에 사용되는 모든 IP 범위에 해당되는 이러한 ARPA 영역은 Microsoft에서 호스트합니다. **이 문서의 나머지 부분은 이 시나리오를 자세히 설명합니다.**


<!--HONumber=Nov16_HO3-->


