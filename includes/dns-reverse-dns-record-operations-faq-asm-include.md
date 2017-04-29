
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>FAQ - Azure 할당 IP 주소에 대한 역방향 DNS

### <a name="how-much-do-reverse-dns-records-cost"></a>역방향 DNS 레코드의 비용은 얼마인가요?

무료입니다.  역방향 DNS 레코드 또는 쿼리에 대한 추가 비용은 없습니다.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>인터넷에서 내 역방향 DNS 레코드가 확인되나요?

예. 클라우드 서비스에 대해 역방향 DNS 속성을 설정하면 역방향 DNS 레코드를 모든 인터넷 사용자에 대해 확인하는 데 필요한 DNS 영역 및 DNS 위임을 Azure에서 모두 관리합니다.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>내 클라우드 서비스에 대해 기본 역방향 DNS 레코드가 생성되나요?

아니요. 역방향 DNS는 옵트인(opt in) 기능입니다. 역방향 레코드를 구성하지 않으면 기본 역방향 DNS 레코드가 만들어지지 않습니다.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>FQDN(정규화된 도메인 이름)의 형식은 무엇인가요?

FQDN은 정방향 순서로 지정되고 점으로 끝나야 합니다(예: "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>내가 지정한 역방향 DNS의 유효성 검사가 실패하면 어떻게 되나요?

역방향 DNS에 대한 유효성 검사가 실패하는 경우 서비스 관리 작업이 실패합니다. 필요에 따라 역방향 DNS 값을 수정하고 다시 시도하세요.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Azure 웹 사이트에 대해 역방향 DNS를 관리할 수 있나요?

역방향 DNS는 Azure 웹 사이트에 대해 지원되지 않습니다. 역방향 DNS는 Azure PaaS 역할 및 IaaS 가상 컴퓨터에 대해 지원됩니다.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>내 클라우드 서비스에 대해 다중 역방향 DNS 레코드를 구성할 수 있나요?

아니요. Azure는 각 Azure 클라우드 서비스에 대해 단일 역방향 DNS 레코드를 지원합니다. 그러나 각 Azure 클라우드 서비스는 자체 역방향 DNS 레코드를 가질 수 있습니다.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Azure 계산 서비스에서 외부 도메인으로 전자 메일을 보낼 수 있나요?

아니요. [Azure Compute Services는 외부 도메인으로의 전자 메일 전송을 지원하지 않습니다](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
