---
title: Azure 개인 링크 자주 묻는 질문(FAQ)
description: Azure 개인 링크에 대해 자세히 알아보세요.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349930"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 개인 링크 자주 묻는 질문(FAQ)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure 개인 엔드포인트 및 Azure 개인 링크 서비스는 무엇입니까?

- **[Azure 개인 끝점](private-endpoint-overview.md)**: Azure 개인 끝점은 Azure 개인 링크에서 제공하는 서비스에 개인적으로 안전하게 연결하는 네트워크 인터페이스입니다. 개인 끝점을 사용하여 개인 링크를 지원하는 Azure PaaS 서비스 또는 사용자 고유의 개인 링크 서비스에 연결할 수 있습니다.
- **[Azure 개인 링크 서비스](private-link-service-overview.md)**: Azure 개인 링크 서비스는 서비스 공급자가 만든 서비스입니다. 현재 개인 링크 서비스는 표준 로드 밸러서의 프런트 엔드 IP 구성에 연결할 수 있습니다. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>비공개 링크를 사용할 때 트래픽이 어떻게 전송되나요?
트래픽은 Microsoft 백본을 사용하여 개인적으로 전송됩니다. 그것은 인터넷을 통과하지 않습니다.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>서비스 끝점과 프라이빗 엔드포인트의 차이점은 무엇입니까?
- 프라이빗 엔드포인트를 사용하는 경우, 네트워크 액세스는 세분화된 세분화를 제공하는 지정된 서비스 뒤에 있는 특정 리소스에 부여되며, 또한 트래픽은 공용 끝점을 사용하지 않고 온-프레미스에서 서비스 리소스에 도달할 수 있습니다.
- 서비스 끝점은 공개적으로 라우팅가능한 IP 주소로 남아 있습니다.  개인 끝점은 개인 끝점이 구성된 가상 네트워크의 주소 공간에 있는 개인 IP입니다.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>프라이빗 링크 서비스와 프라이빗 엔드포인트의 관계는 무엇입니까?
개인 엔드포인트는 Azure PaaS 서비스 및 사용자 고유의 개인 링크 서비스를 포함하여 여러 개인 링크 리소스 유형에 대한 액세스를 제공합니다. 그것은 일대다 관계입니다. 하나의 개인 링크 서비스는 여러 개인 끝점에서 연결을 수신할 수 있습니다. 반면에 하나의 개인 끝점은 하나의 개인 링크 서비스에만 연결할 수 있습니다.    

## <a name="private-endpoint"></a>Private Endpoint 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>동일한 VNet에서 여러 개의 비공개 엔드포인트를 만들 수 있습니까? 다른 서비스에 연결할 수 있습니까? 
예. 동일한 VNet 또는 서브넷에 여러 개의 개인 끝점을 가질 수 있습니다. 그들은 다른 서비스에 연결할 수 있습니다.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>전용 끝점에 전용 서브넷이 필요합니까? 
아니요. 전용 끝점에 전용 서브넷이 필요하지 않습니다. 서비스가 배포되는 VNet의 모든 서브넷에서 개인 엔드포인트 IP를 선택할 수 있습니다.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>프라이빗 엔드포인트가 Azure Active Directory 테넌트 전체에서 개인 링크 서비스에 연결할 수 있습니까? 
예. 개인 끝점은 AD 테넌트 전체에서 개인 링크 서비스 또는 Azure PaaS에 연결할 수 있습니다.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>개인 엔드포인트가 Azure 리전에서 Azure PaaS 리소스에 연결할 수 있습니까?
예. 개인 끝점은 Azure 리전에서 Azure PaaS 리소스에 연결할 수 있습니다.

## <a name="private-link-service"></a>Private Link Service
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>개인 링크 서비스를 만들기 위한 전제 조건은 무엇입니까? 
서비스 백엔드는 가상 네트워크와 표준 로드 밸러서 뒤에 있어야 합니다.
 
### <a name="how-can-i-scale-my-private-link-service"></a>개인 링크 서비스를 확장하면 어떻게 하나요? 
다음과 같은 몇 가지 방법으로 개인 링크 서비스를 확장할 수 있습니다. 
- 표준 로드 밸러블러 뒤의 풀에 백엔드 VM 추가 
- 개인 링크 서비스에 IP를 추가합니다. 프라이빗 링크 서비스당 최대 8개의 IP를 허용합니다.  
- 표준 로드 밸러블러에 새 개인 링크 서비스를 추가합니다. 로드 밸러워당 최대 8개의 프라이빗 링크 서비스를 허용합니다.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>서비스를 여러 개인 엔드포인트에 연결할 수 있습니까?
예. 하나의 개인 링크 서비스는 여러 개인 엔드포인트에서 연결을 수신할 수 있습니다. 그러나 하나의 개인 엔드포인트는 하나의 개인 링크 서비스에만 연결할 수 있습니다.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>개인 링크 서비스의 노출을 어떻게 제어해야 하나요?
개인 링크 서비스의 가시성 구성을 사용하여 노출을 제어할 수 있습니다. 가시성은 세 가지 설정을 지원합니다.

- **없음** - RBAC 액세스 권한이 있는 구독만 서비스를 찾을 수 있습니다. 
- **제한** - 제외 목록에 있고 RBAC 액세스 권한이 있는 구독만 서비스를 찾을 수 있습니다. 
- **모두** - 모든 사람이 서비스를 찾을 수 있습니다. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>기본 로드 밸러서를 사용하여 개인 링크 서비스를 만들 수 있습니까? 
아니요. 기본 로드 밸러서에 대한 개인 링크 서비스는 지원되지 않습니다.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>개인 링크 서비스에 전용 서브넷이 필요합니까? 
아니요. 개인 링크 서비스에는 전용 서브넷이 필요하지 않습니다. 서비스가 배포되는 VNet에서 모든 서브넷을 선택할 수 있습니다.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure 개인 링크를 사용하는 서비스 공급자입니다. 모든 고객이 고유한 IP 공간을 가지고 있고 IP 공간과 겹치지 않도록 해야 합니까? 
아니요. Azure 개인 링크는 이 기능을 제공합니다. 따라서 고객의 주소 공간과 겹치지 않는 주소 공간이 필요하지 않습니다. 

##  <a name="next-steps"></a>다음 단계

- Azure [개인 링크에](private-link-overview.md) 대해 알아보기
