---
title: Azure Purview 프라이빗 엔드포인트 FAQ(질문과 대답)
description: 이 문서에는 Azure Purview 프라이빗 엔드포인트에 대해 자주 묻는 질문과 대답이 나와 있습니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 13d9f65a812dcf9cfc45b84758f351e301827173
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104930"
---
# <a name="frequently-asked-questions-faq-about-azure-purview-private-endpoints"></a>Azure Purview 프라이빗 엔드포인트에 대한 질문과 대답(FAQ)

이 문서에는 고객 및 현장 팀이 [Azure Private Link](../private-link/private-link-overview.md)를 사용하는 Azure Purview 네트워크 구성에 대해 자주 묻는 일반적인 질문에 대한 답변이 있습니다. Azure Purview 방화벽 설정, 프라이빗 엔드포인트, DNS 구성 및 관련 구성에 대한 질문을 명확하게 설명하기 위한 것입니다.

프라이빗 링크를 사용하여 Azure Purview를 설정하려면 [Purview 계정에 프라이빗 엔드포인트 사용](./catalog-private-link.md)을 참조하세요.

## <a name="common-questions"></a>일반적인 질문

### <a name="what-is-purpose-of-deploying-azure-purview-account-private-endpoint"></a>Azure Purview '계정' 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?

Azure Purview _계정_ 프라이빗 엔드포인트는 V-net 내에서 발생하는 클라이언트 호출만 계정에 액세스할 수 있는 시나리오를 사용하여 보안 계층을 추가하는 데 사용됩니다. 이 프라이빗 엔드포인트는 포털 프라이빗 엔드포인트에 대한 필수 구성 요소이기도 합니다.
    
### <a name="what-is-purpose-of-deploying--azure-purview-portal-private-endpoint"></a>Azure Purview '포털' 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?
Azure Purview _포털_ 프라이빗 엔드포인트는 Azure Purview Studio에 대한 프라이빗 연결을 제공하기 위한 것입니다.
    
### <a name="what-is-purpose-of-deploying-azure-purview-ingestion-private-endpoints"></a>Azure Purview '수집' 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?

Azure Purview는 수집 프라이빗 엔드포인트를 사용하여 Azure 또는 온-프레미스 환경에서 데이터 원본을 검사할 수 있습니다. 수집 프라이빗 엔드포인트를 만들 때 3가지 추가 프라이빗 엔드포인트 리소스가 배포되고 Azure Purview 관리되는 리소스에 연결됩니다.
- Azure Purview 관리형 스토리지 계정에 연결된 _Blob_ 
- Azure Purview 관리형 스토리지 계정에 연결된 _큐_  
- Azure Purview 관리형 이벤트 허브 네임스페이스에 연결된 _네임스페이스_
     
### <a name="can-i-scan-data-through-public-endpoint-if-private-endpoint-is-enabled-on-my-azure-purview-account"></a>Azure Purview 계정에서 프라이빗 엔드포인트를 사용하도록 설정한 경우 공용 엔드포인트를 통해 데이터를 검사할 수 있나요?

예, 프라이빗 엔드포인트를 통해 연결되지 않은 데이터 원본은 공용 엔드포인트를 사용하여 검사할 수 있습니다. 한편 Azure Purview는 프라이빗 엔드포인트를 사용하도록 구성됩니다.
    
### <a name="can-i-scan-data-through-service-endpoint-if-private-endpoint-is-enabled"></a>프라이빗 엔드포인트가 사용하도록 설정된 경우 서비스 엔드포인트를 통해 데이터를 검사할 수 있나요?

예, 프라이빗 엔드포인트를 통해 연결되지 않은 데이터 원본은 서비스 엔드포인트를 사용하여 검사할 수 있지만 Azure Purview는 프라이빗 엔드포인트를 사용하도록 구성됩니다.
Azure에서 데이터 원본 리소스의 서비스 엔드포인트 구성 내에서 신뢰할 수 있는 Microsoft 서비스가 리소스에 액세스할 수 있도록 허용을 사용하도록 설정해야 합니다. 예를 들어 방화벽 및 가상 네트워크 설정이 _선택된 네트워크_ 로 지정된 Azure Blob Storage를 검사하려는 경우 _신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용_ 을 예외로 선택해야 합니다. 
    
###  <a name="can-i-access-azure-purview-studio-from-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>공용 네트워크 액세스가 Azure Purview 계정 네트워킹에서 거부로 설정된 경우 공용 네트워크에서 Azure Purview Studio에 액세스할 수 있나요?

아니요. 공용 엔드포인트 _공용 네트워크 액세스_ 에서 Azure Purview에 연결이 _거부_ 로 설정되면 다음과 같은 오류 메시지가 표시됩니다.

_이 Purview 계정에 액세스할 권한이 없음_
_이 Purview 계정은 프라이빗 엔드포인트 뒤에 있습니다. Purview 계정의 프라이빗 엔드포인트에 대해 구성된 같은 VNet(가상 네트워크)의 클라이언트에서 계정에 액세스하세요._

이 경우 Azure Purview Studio를 시작하려면 Azure Purview 포털 프라이빗 엔드포인트와 동일한 VNet에 배포된 컴퓨터를 사용하거나 하이브리드 연결이 허용되는 CorpNet에 연결된 VM을 사용해야 합니다.

### <a name="is-it-possible-to-restrict-access-to-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-end-users-across-the-web"></a>Azure Purview 관리형 스토리지 계정 및 Event Hub 네임스페이스에 대한 액세스를 제한할 수 있지만(프라이빗 엔드포인트 검색에만 해당) 웹을 통해 최종 사용자에 대해 포털 액세스를 사용하도록 유지할 수 있나요?

아니요. _공용 네트워크 액세스_ 를 _거부_ 로 설정하면 Azure Purview 관리형 스토리지 계정 및 Event Hub 네임스페이스에 대한 액세스가 프라이빗 엔드포인트 수집에 대해서만 자동으로 설정됩니다. _공용 네트워크 액세스_ 를 _허용_ 으로 설정하면 Azure Purview 관리형 스토리지 계정 및 Event Hub 네임스페이스에 대한 액세스가 _모든 네트워크_ 에 대해 자동으로 설정됩니다. 수동으로 관리형 스토리지 계정 또는 Event Hub 네임스페이스에 대한 프라이빗 엔드포인트 수집을 수정할 수 없습니다.
    
### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-can-be-publicly-accessible"></a>공용 네트워크 액세스가 허용으로 설정된 경우 이는 관리형 스토리지 계정 및 Event Hub를 공개적으로 액세스할 수 있음을 의미하나요?

아니요. 보호된 리소스로 Azure Purview 관리형 스토리지 계정 및 Event Hub 네임스페이스에 대한 액세스는 Azure Purview로만 제한됩니다. 이러한 리소스는 모든 애플리케이션, 사용자 또는 그룹이 액세스할 수 없도록 하는 모든 보안 주체에 대해 거부 할당을 사용하여 배포됩니다.

Azure 거부 할당에 대한 자세한 내용은 [Azure 거부 할당 이해](../role-based-access-control/deny-assignments.md)를 참조하세요.
    
### <a name="what-are-the-supported-authentication-type-when-using-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 경우 지원되는 인증 형식은 무엇인가요?

Azure Key Vault 또는 서비스 주체.
  
### <a name="what-are-private-dns-zones-required-for-azure-purview-for-private-endpoint"></a>프라이빗 엔드포인트에 대한 Azure Purview에 필요한 프라이빗 DNS 영역은 무엇인가요?

**Azure Purview 리소스:** 
- `privatelink.purview.azure.com`

**Azure Purview 관리되는 리소스의 경우:**
- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`
    
### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-deploying-azure-purview-private-endpoints"></a>Azure Purview 프라이빗 엔드포인트를 배포할 때 전용 Virtual Network와 전용 서브넷을 사용해야 하나요?

아니요, 그러나 프라이빗 엔드포인트를 배포하기 전에 대상 서브넷에서 _PrivateEndpointNetworkPolicies_ 를 사용하지 않도록 설정해야 합니다.
데이터 원본을 프레미스 간에 검사하려는 경우 VNet 피어링을 통해 데이터 원본 VNet에 대한 네트워크 연결 및 온-프레미스 네트워크에 대한 액세스가 있는 가상 네트워크에 Azure Purview를 배포하는 것이 좋습니다.
    
[프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)하는 방법을 알아봅니다.

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>Azure Purview 프라이빗 엔드포인트를 배포하고 내 구독의 기존 프라이빗 DNS 영역을 사용하여 A 레코드를 등록할 수 있나요?

예. Azure Purview 및 모든 데이터 원본 레코드에 필요한 모든 내부 DNS 영역에 대한 허브 또는 데이터 관리 구독에서 프라이빗 엔드포인트 DNS 영역을 중앙 집중화할 수 있습니다. 이는 Azure Purview에서 프라이빗 엔드포인트 내부 IP 주소를 사용하여 데이터 원본을 확인할 수 있도록 권장하는 방법입니다.

또한 기존 프라이빗 DNS 영역에 대한 VNet을 위해 [가상 네트워크 링크](../dns/private-dns-virtual-network-links.md)를 설정해야 합니다.
    
### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-private-endpoint"></a>Azure Integration Runtime을 사용하여 프라이빗 엔드포인트를 통해 데이터 원본을 검사할 수 있나요? 

아니요. 프라이빗 연결을 사용하여 데이터를 검사하려면 자체 호스팅 통합 런타임을 배포하고 등록해야 합니다. Azure Key Vault 또는 서비스 주체는 데이터 원본에 대한 인증 방법으로 사용해야 합니다.
    
### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-using-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 경우 Azure Purview에 대한 자체 호스팅 통합 런타임이 있는 가상 머신에 대한 아웃바운드 포트 및 방화벽 요구 사항은 무엇인가요?

자체 호스팅 통합 런타임이 배포되는 VM은 포트 443을 통해 Azure 엔드포인트 및 Azure Purview 개인 IP 주소에 대한 아웃바운드 액세스 권한이 있어야 합니다. 
    
### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-private-endpoint-is-enabled"></a>프라이빗 엔드포인트를 사용하는 경우 자체 호스팅 통합 런타임을 실행하는 가상 머신에서 아웃바운드 인터넷 액세스를 사용하도록 설정해야 하나요?

아니요. 그러나 자체 호스팅 통합 런타임을 실행하는 가상 머신은 포트 443을 사용하여 내부 IP 주소를 통해 Azure Purview에 연결할 수 있습니다. 문제 해결을 위해 nslookup.exe 및 Test-NetConnection과 같은 이름 확인 및 연결 테스트에 일반적인 도구를 사용합니다.
    
### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>내 컴퓨터에서 Azure Purview Studio를 시작하려고 할 때 다음 오류 메시지가 수신된 이유는 무엇인가요?

_이 Purview 계정은 프라이빗 엔드포인트 뒤에 있습니다. Purview 계정의 프라이빗 엔드포인트에 대해 구성된 같은 VNet(가상 네트워크)의 클라이언트에서 계정에 액세스하세요._

Azure Purview 계정이 Azure Private Link를 사용하여 배포되고 Azure Purview 계정에서 공용 액세스가 사용하지 않도록 설정되었을 수 있으므로 Azure Purview에 대한 내부 네트워크 연결이 있는 가상 머신에서 Azure Purview Studio를 검색해야 합니다.

하이브리드 네트워크 뒤의 VM에서 연결하거나 VNet에 연결된 점프 머신을 사용하는 경우 이름 확인 및 연결 테스트(예: nslookup.exe 및 Test-NetConnection)에 일반적인 문제 해결 도구를 사용합니다.

1. Azure Purview 계정의 개인 IP 주소를 통해 다음 주소를 확인할 수 있는지 확인합니다.

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

2. 다음 PowerShell 명령을 사용하여 Azure Purview 계정에 대한 네트워크 연결을 확인합니다.

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

3. 사용자 고유의 DNS 확인 인프라를 사용하는 경우 크로스-프레미스 DNS 구성을 확인합니다. 

   프라이빗 엔드포인트의 DNS 설정에 대한 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

프라이빗 링크를 사용하여 Azure Purview를 설정하려면 [Purview 계정에 프라이빗 엔드포인트 사용](./catalog-private-link.md)을 참조하세요.
