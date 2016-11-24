
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>FAQ - Azure DNS에서 ARPA 영역 호스팅

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Azure DNS에서 내 ISP가 할당한 IP 블록에 대한 ARPA 영역을 호스트할 수 있나요?

 예. Azure DNS에서 고유한 IP 범위에 대해 ARPA 영역을 호스팅하는 것은 완전히 지원됩니다.

단순히 [Azure DNS에서 영역을 만든 후](../articles/dns/dns-getstarted-create-dnszone.md), ISP 작업으로 [영역을 위임](../articles/dns/dns-domain-delegation.md)합니다.  그런 다음 다른 레코드 유형과 동일한 방식으로 각 역방향 조회를 위해 PTR 레코드를 관리할 수 있습니다.

또한 [Azure CLI를 사용하여 기존 역방향 조회 영역을 가져올 수도 있습니다](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>내 ARPA 영역을 호스팅하는 비용은 얼마인가요?

Azure DNS에서 ISP가 할당한 IP 블록에 대해 ARPA 영역을 호스팅하는 데는 [표준 Azure DNS 요금](https://azure.microsoft.com/pricing/details/dns/)이 청구됩니다.

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Azure DNS에서 IPv4 및 IPv6 주소 모두에 대해 ARPA 영역을 호스팅할 수 있나요?

 예.


<!--HONumber=Nov16_HO3-->


