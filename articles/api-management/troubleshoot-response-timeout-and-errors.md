---
title: API Management의 클라이언트 응답 시간 제한 및 오류 문제 해결
description: API Management에서 간헐적 연결 오류 및 관련 된 대기 시간 문제 해결
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576515"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>API Management의 클라이언트 응답 시간 제한 및 오류 문제 해결

이 문서는 [Azure API Management](./api-management-key-concepts.md)에서 간헐적 연결 오류 및 관련 된 대기 시간 문제를 해결 하는 데 도움이 됩니다. 특히이 문서에서는 SNAT (원본 주소 네트워크 변환) 포트가 소모 될 때 정보를 제공 하 고 문제를 해결 합니다. 도움이 더 필요한 경우 azure [커뮤니티 지원](https://azure.microsoft.com/support/community/) 에서 azure 전문가에 게 문의 하거나 [azure 지원](https://azure.microsoft.com/support/options/)에 대 한 지원 요청을 파일 하세요.

## <a name="symptoms"></a>증상

APIM (API Management) 서비스를 통해 Api를 호출 하는 클라이언트 응용 프로그램은 다음 증상 중 하나 이상을 나타낼 수 있습니다.

* 일시적인 HTTP 500 오류
* 시간 제한 오류 메시지

이러한 증상은 `BackendConnectionFailure` [Azure Monitor 리소스 로그](../azure-monitor/essentials/resource-logs.md)에서의 인스턴스로 매니페스트 됩니다.

## <a name="cause"></a>원인

이 증상은 APIM 서비스를 사용 하 여 SNAT (네트워크 주소 변환) 포트 제한으로 인해 발생 하는 경우가 많습니다.

클라이언트가 APIM Api 중 하나를 호출할 때마다 Azure API Management 서비스는 백 엔드 API에 액세스 하는 SNAT 포트를 엽니다. Azure [의 아웃 바운드 연결](../load-balancer/load-balancer-outbound-connections.md)에 설명 된 대로, AZURE는 SNAT (원본 네트워크 주소 변환) 및 Load Balancer (고객에 게 노출 되지 않음)를 사용 하 여 공용 IP 주소 공간에 있는 azure 외부의 끝점과 통신 하 고 [Virtual Network 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)을 사용 하지 않는 azure 내부의 끝점을 통신 합니다. 이 상황은 공용 Ip에 노출 된 백 엔드 Api에만 적용 됩니다.

처음에는 API Management 서비스의 각 인스턴스에 미리 할당 된 SNAT 포트 수가 지정 됩니다. 이 제한은 동일한 호스트 및 포트 조합에 대 한 열린 연결에 영향을 줍니다. SNAT 포트는 동일한 주소 및 포트 조합에 대 한 호출이 반복 될 때 사용 됩니다. SNAT 포트가 해제 되 면 필요에 따라 포트를 재사용할 수 있습니다. Azure 네트워크 부하 분산 장치는 4 분 동안 대기한 후에만 닫힌 연결에서 SNAT 포트를 회수 합니다.

Api에 대 한 클라이언트 요청을 신속 하 게 연속 하 게 재생 하면 해당 포트가 닫혀 있지 않고 충분히 빠르게 재생 되지 않아 APIM 서비스에서 적시에 클라이언트 요청을 처리할 수 없는 경우 SNAT 포트의 미리 할당 된 할당량이 고갈 될 수 있습니다.

## <a name="mitigations-and-solutions"></a>완화 및 해결 방법

먼저 SNAT 포트 소모 문제를 해결 하려면 백 엔드 서비스의 성능을 진단 하 고 최적화 해야 합니다.

SNAT 포트 소모를 완화 하기 위한 일반적인 전략은 *Azure Load Balancer* 설명서의 [아웃 바운드 연결 오류 문제 해결](../load-balancer/troubleshoot-outbound-connection.md) 에 설명 되어 있습니다. 이러한 전략 중에서 다음은 API Management에 적용 됩니다.

### <a name="scale-your-apim-instance"></a>APIM 인스턴스 크기 조정

각 API Management 인스턴스에는 APIM 단위에 따라 여러 SNAT 포트가 할당 됩니다. 추가 단위를 사용 하 여 API Management 인스턴스 크기를 조정 하 여 추가 SNAT 포트를 할당할 수 있습니다. 자세한 내용은 [API Management 서비스 크기 조정](upgrade-and-scale.md#scale-your-api-management-service) 을 참조 하세요.

> [!NOTE]
> SNAT 포트 사용은 현재 자동 크기 조정 API Management 단위에 대 한 메트릭으로 사용할 수 없습니다.

### <a name="use-multiple-ips-for-your-backend-urls"></a>백 엔드 Url에 여러 Ip 사용

APIM 인스턴스에서 백 엔드 서비스의 동일한 대상 IP 및 대상 포트에 연결 하는 각 연결에는 고유한 트래픽 흐름을 유지 하기 위해 SNAT 포트가 사용 됩니다. 백그라운드 서비스의 반환 트래픽에 대해 다른 SNAT 포트를 사용 하지 않는 경우 APIM은 한 응답을 다른 응답으로 구분할 수 없습니다.

SNAT 포트는 대상 IP 또는 대상 포트가 다를 때 재사용할 수 있으므로 SNAT 포트 소모를 방지 하는 또 다른 방법은 백 엔드 서비스 Url에 대해 여러 Ip를 사용 하는 것입니다.

자세한 내용은 [아웃 바운드 프록시 Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md)를 참조 하세요.

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>동일한 VNet에 APIM 및 백 엔드 서비스를 저장 합니다.

백 엔드 API가 App Service와 같은 *서비스 끝점* 을 지 원하는 Azure 서비스에서 호스트 되는 경우 apim 인스턴스와 백 엔드 서비스를 동일한 가상 네트워크에 배치 하 고 [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [개인 끝점](../private-link/private-endpoint-overview.md)을 통해 노출 하 여 SNAT 포트 소모 문제를 방지할 수 있습니다. 공용 VNet을 사용 하 고 통합 서브넷에 서비스 끝점을 배치한 경우 APIM 인스턴스에서 해당 서비스로의 아웃 바운드 트래픽은 인터넷을 우회 하 여 SNAT 포트 제한을 방지 합니다. 마찬가지로 VNet 및 개인 끝점을 사용 하는 경우 해당 대상에 대 한 아웃 바운드 SNAT 포트 문제가 발생 하지 않습니다.

자세한 내용은 [가상 네트워크에서 azure API Management를 사용 하는 방법](api-management-using-with-vnet.md) 및 [azure 가상 네트워크와 App Service 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조 하세요.

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>가상 네트워크에 APIM을 추가 하 고 아웃 바운드 호출을 Azure 방화벽으로 라우팅합니다.

가상 네트워크에 APIM 및 백 엔드 서비스를 배치 하는 것과 마찬가지로, APIM 서비스를 사용 하 여 VNet에서 Azure 방화벽을 사용한 다음 아웃 바운드 APIM 호출을 Azure 방화벽으로 라우팅할 수 있습니다. APIM과 Azure 방화벽 (동일한 VNet에 있는) 간에는 SNAT 포트가 필요 하지 않습니다. 백 엔드 서비스에 대 한 SNAT 연결의 경우 Azure 방화벽에는 APIM 인스턴스에 할당 된 것 보다 훨씬 더 높은 64000의 포트가 있습니다.

자세한 내용은 [Azure 방화벽](../firewall/overview.md) 설명서를 참조 하세요.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>응답 캐싱 및 기타 백엔드 성능 튜닝 고려

고려해 야 할 또 다른 잠재적 완화 방법은 백 엔드 Api의 처리 시간을 개선 하는 것입니다. 이 작업을 수행 하는 한 가지 방법은 API를 호출 하는 클라이언트 응용 프로그램과 APIM 백 엔드 로드 사이의 대기 시간을 줄이기 위해 응답 캐싱에 특정 Api를 구성 하는 것입니다.

자세한 내용은 [Azure API Management의 성능 향상을 위해 캐싱 추가](api-management-howto-cache.md)를 참조 하세요.

### <a name="consider-implementing-access-restriction-policies"></a>액세스 제한 정책 구현 고려

비즈니스 시나리오에 적합 한 경우 API Management 제품에 대 한 액세스 제한 정책을 구현할 수 있습니다. 예를 들어 `rate-limit-by-key` 정책을 사용 하 여 지정 된 기간별 호출 속도로 제한 함으로써 키 단위로 API 사용 급증을 방지할 수 있습니다.

자세한 내용은 [API Management 액세스 제한 정책](api-management-access-restriction-policies.md) 을 참조 하세요.

## <a name="see-also"></a>참고 항목

* [Azure Load Balancer: 아웃 바운드 연결 오류 문제 해결](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: 간헐적인 아웃 바운드 연결 오류 문제 해결](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
