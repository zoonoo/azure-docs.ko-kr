---
title: Azure Private Link FAQ(질문과 대답)
description: Azure 개인 링크에 대해 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496476"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link FAQ(질문과 대답)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure 개인 끝점 및 Azure 개인 링크 서비스 란 무엇 인가요?

- **[Azure 개인 끝점](private-endpoint-overview.md)**: Azure 개인 끝점은 Azure 개인 링크를 통해 제공 되는 서비스에 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 개인 끝점을 사용 하 여 개인 링크 또는 개인 링크 서비스에 대 한 개인 링크를 지 원하는 Azure PaaS 서비스에 연결할 수 있습니다.
- **[Azure 개인 링크 서비스](private-link-service-overview.md)**: Azure 개인 링크 서비스는 서비스 공급자가 만든 서비스입니다. 현재 개인 링크 서비스는 표준 Load Balancer의 프런트 엔드 IP 구성에 연결할 수 있습니다. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>개인 링크를 사용 하는 경우 트래픽이 어떻게 전송 되나요?
트래픽은 Microsoft 백본을 통해 개인적으로 전송 됩니다. 인터넷을 통과 하지 않습니다. Azure 개인 링크는 고객 데이터를 저장 하지 않습니다.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>서비스 끝점과 개인 끝점의 차이점은 무엇 인가요?
- 개인 끝점은 세분화 된 분할을 제공 하는 특정 서비스 뒤의 특정 리소스에 대 한 네트워크 액세스를 허용 합니다. 트래픽은 공용 끝점을 사용 하지 않고 온-프레미스에서 서비스 리소스에 도달할 수 있습니다.
- 서비스 끝점은 공개적으로 라우팅 가능한 IP 주소를 유지 합니다.  개인 끝점은 개인 끝점이 구성 된 가상 네트워크의 주소 공간에 있는 개인 IP입니다.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>개인 링크 서비스와 개인 끝점 간의 관계는 무엇 인가요?
여러 개인 링크 리소스 형식은 개인 끝점을 통해 액세스를 지원 합니다. 리소스에는 Azure PaaS 서비스와 자신의 개인 링크 서비스가 포함 됩니다. 일 대 다 관계입니다. 

개인 링크 서비스는 여러 개인 끝점에서 연결을 받습니다. 개인 끝점은 하나의 개인 링크 서비스에 연결 합니다.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>개인 링크에 대 한 네트워크 정책을 사용 하지 않도록 설정 해야 하나요?
예. 개인 끝점과 개인 링크 서비스 모두 제대로 작동 하려면 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. 둘 다 서로 독립적인 속성이 있습니다.

## <a name="private-endpoint"></a>Private Endpoint 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>동일한 VNet에 여러 개인 끝점을 만들 수 있나요? 서로 다른 서비스에 연결할 수 있나요? 
예. 동일한 VNet 또는 서브넷에 여러 개인 끝점을 포함할 수 있습니다. 서로 다른 서비스에 연결할 수 있습니다.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>전용 끝점에 전용 서브넷이 필요 한가요? 
아니요. 전용 끝점에 전용 서브넷이 필요 하지 않습니다. 서비스가 배포 된 VNet의 모든 서브넷에서 개인 끝점 IP를 선택할 수 있습니다.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>개인 끝점이 Azure Active Directory 테 넌 트 간 개인 링크 서비스에 연결할 수 있나요? 
예. 개인 끝점은 개인 링크 서비스 또는 Azure Active Directory 테 넌 트 간 Azure PaaS에 연결할 수 있습니다. 테 넌 트 간에 연결 하는 개인 끝점은 수동 요청 승인이 필요 합니다. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>개인 끝점이 Azure 지역에서 Azure PaaS 리소스에 연결할 수 있나요?
예. 개인 끝점은 Azure 지역에서 Azure PaaS 리소스에 연결할 수 있습니다.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>내 개인 끝점 NIC (네트워크 인터페이스)를 수정할 수 있나요?
개인 끝점을 만들 때 읽기 전용 NIC가 할당 됩니다. 이는 수정할 수 없으며 개인 끝점의 수명 주기 동안 유지 됩니다.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>지역 장애가 발생 하는 경우 개인 끝점을 사용 하는 동안 가용성을 달성할 어떻게 할까요? 있나요?

전용 끝점은 99.99% SLA [[Azure 개인 링크에 대 한 sla]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)를 사용 하는 항상 사용 가능한 리소스입니다. 그러나 이러한 리소스는 지역 리소스 이므로 Azure 지역 중단이 가용성에 영향을 줄 수 있습니다. 지역별 장애가 발생 하는 경우 가용성을 얻기 위해 동일한 대상 리소스에 연결 된 여러 Pe를 서로 다른 지역에 배포할 수 있습니다. 이러한 방식으로 한 지역이 다운 되 면 다른 지역의 PE를 통해 복구 시나리오에 대 한 트래픽을 라우팅할 수 있으므로 대상 리소스에 액세스할 수 있습니다. 대상 서비스 쪽에서 지역 장애를 처리 하는 방법에 대 한 자세한 내용은 장애 조치 (failover) 및 복구에 대 한 서비스 설명서를 검토 하세요. 개인 링크 트래픽은 대상 끝점에 대 한 Azure DNS 확인을 따릅니다. 


## <a name="private-link-service"></a>Private Link Service
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>개인 링크 서비스를 만들기 위한 필수 구성 요소는 무엇 인가요? 
Service 백 엔드는 가상 네트워크에 있고 표준 Load Balancer 뒤에 있어야 합니다.
 
### <a name="how-can-i-scale-my-private-link-service"></a>개인 링크 서비스의 크기를 조정 하려면 어떻게 해야 하나요? 
여러 가지 방법으로 개인 링크 서비스를 확장할 수 있습니다. 
- 표준 Load Balancer 뒤에 백 엔드 Vm을 풀에 추가 합니다. 
- 개인 링크 서비스에 IP를 추가 합니다. 개인 링크 서비스 당 최대 8 개의 Ip를 허용 합니다.  
- 표준 Load Balancer에 새 개인 링크 서비스를 추가 합니다. 부하 분산 장치 당 최대 8 개의 개인 링크 서비스를 허용 합니다.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>개인 링크 서비스에서 사용 되는 NAT (네트워크 주소 변환) IP 구성은 무엇입니까? 사용 가능한 포트 및 연결을 기준으로 크기를 조정 하려면 어떻게 해야 하나요? 

NAT IP 구성을 사용 하면 대상 쪽 (서비스 공급자 측)의 개인 링크 트래픽에 원본 NAT를 제공 하 여 원본 (소비자 쪽)과 대상 (서비스 공급자) 주소 공간 간에 IP 충돌이 발생 하지 않도록 합니다. NAT IP 주소는 서비스에서 받은 모든 패킷에 대해 원본 IP로 표시 되 고, 서비스에서 보낸 모든 패킷에 대해 대상 IP로 표시 됩니다.  NAT IP는 서비스 공급자의 가상 네트워크에 있는 모든 서브넷에서 선택할 수 있습니다. 

각 NAT IP는 표준 Load Balancer 뒤에 VM 당 64k TCP 연결 (64k 포트)을 제공 합니다. 더 많은 연결을 확장 하 고 추가 하기 위해 새 NAT Ip를 추가 하거나 표준 Load Balancer 뒤에 Vm을 더 추가할 수 있습니다. 이렇게 하면 포트 가용성이 조정 되 고 더 많은 연결이 허용 됩니다. 연결은 NAT Ip와 표준 Load Balancer 뒤에 있는 Vm 간에 분산 됩니다.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>내 서비스를 여러 개인 끝점에 연결할 수 있나요?
예. 하나의 개인 링크 서비스는 여러 개인 끝점에서 연결을 수신할 수 있습니다. 그러나 하나의 개인 끝점은 하나의 개인 링크 서비스에만 연결할 수 있습니다.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>개인 링크 서비스의 노출을 제어 하려면 어떻게 해야 하나요?
개인 링크 서비스에서 표시 유형 구성을 사용 하 여 노출을 제어할 수 있습니다. 표시 유형은 세 가지 설정을 지원 합니다.

- **None** -Azure RBAC 액세스 권한이 있는 구독만 서비스를 찾을 수 있습니다. 
- 승인 된 **제한적** 전용 구독 및 Azure RBAC 액세스 권한으로 서비스를 찾을 수 있습니다. 
- **모두** -모든 사용자가 서비스를 찾을 수 있습니다. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>기본 부하 분산 장치를 사용 하 여 개인 링크 서비스를 만들 수 있나요? 
아니요. 기본 부하 분산 장치를 통한 개인 링크 서비스는 지원 되지 않습니다.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>개인 링크 서비스에 전용 서브넷이 필요 한가요? 
아니요. 개인 링크 서비스에는 전용 서브넷이 필요 하지 않습니다. VNet에서 서비스가 배포 된 모든 서브넷을 선택할 수 있습니다.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure 개인 링크를 사용 하는 서비스 공급자입니다. 모든 고객에 게 고유한 IP 공간이 있는지와 IP 공간이 겹치지 않도록 해야 하나요? 
아니요. Azure 개인 링크는이 기능을 제공 합니다. 고객의 주소 공간에 겹치지 않는 주소 공간이 있을 필요는 없습니다. 

##  <a name="next-steps"></a>다음 단계

- [Azure Private Link](private-link-overview.md)에 대해 알아보기
