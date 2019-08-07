---
title: Azure Functions 네트워킹 옵션
description: Azure Functions에서 사용할 수 있는 모든 네트워킹 옵션에 대 한 개요
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f4f081001f2573bccc58205ccc7955739b7f5c4c
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779286"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 네트워킹 옵션

이 문서에서는 Azure Functions에 대 한 호스팅 옵션에서 사용할 수 있는 네트워킹 기능을 설명 합니다. 다음 네트워킹 옵션은 모두 인터넷 라우팅 가능 주소를 사용 하지 않고 리소스에 액세스 하는 기능을 제공 하거나 기능 앱에 대 한 인터넷 액세스를 제한 합니다. 

호스팅 모델은 서로 다른 수준의 네트워크 격리를 사용할 수 있습니다. 올바른 항목을 선택 하면 네트워크 격리 요구 사항을 충족 하는 데 도움이 됩니다.

다음 두 가지 방법으로 함수 앱을 호스트할 수 있습니다.

* 다양 한 수준의 가상 네트워크 연결 및 확장 옵션을 사용 하 여 다중 테 넌 트 인프라에서 실행 되는 계획 옵션 집합이 있습니다.
    * [소비 계획](functions-scale.md#consumption-plan)-로드에 대 한 응답으로 동적으로 확장 되 고 최소 네트워크 격리 옵션을 제공 합니다.
    * 더 포괄적인 네트워크 격리를 제공 하면서도 동적으로 확장 되는 [프리미엄 요금제](functions-scale.md#premium-plan).
    * Azure [App Service 요금제](functions-scale.md#app-service-plan)는 고정 된 규모에서 작동 하 고 프리미엄 요금제와 비슷한 네트워크 격리를 제공 합니다.
* [App Service Environment](../app-service/environment/intro.md)에서 함수를 실행할 수 있습니다. 이 메서드는 가상 네트워크에 함수를 배포 하 고 전체 네트워크 제어 및 격리를 제공 합니다.

## <a name="matrix-of-networking-features"></a>네트워킹 기능 매트릭스

|                |[소비 계획](functions-scale.md#consumption-plan)|[프리미엄 요금제 (미리 보기)](functions-scale.md#premium-plan)|[App Service 계획](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[인바운드 IP 제한 & 개인 사이트 액세스](#inbound-ip-restrictions)|예 ✅|예 ✅|예 ✅|예 ✅|
|[가상 네트워크 통합](#virtual-network-integration)|❌ 아니요|✅ 예 (지역)|✅ 예 (지역 및 게이트웨이)|예 ✅|
|[가상 네트워크 트리거 (비 HTTP)](#virtual-network-triggers-non-http)|❌ 아니요| ❌ 아니요|예 ✅|예 ✅|
|[VNet](#hybrid-connections)|❌ 아니요|❌ 아니요|예 ✅|예 ✅|
|[아웃 바운드 IP 제한](#outbound-ip-restrictions)|❌ 아니요| ❌ 아니요|❌ 아니요|예 ✅|


## <a name="inbound-ip-restrictions"></a>인바운드 IP 제한

IP 제한을 사용 하 여 앱에 대 한 액세스가 허용 되거나 거부 되는 IP 주소의 우선 순위를 지정 하는 목록을 정의할 수 있습니다. 목록은 IPv4 및 IPv6 주소를 포함할 수 있습니다. 항목이 하나 이상 있는 경우 목록 끝에 암시적 "deny all"이 있습니다. IP 제한은 모든 함수 호스팅 옵션에서 작동 합니다.

> [!NOTE]
> 네트워크 제한을 사용 하는 경우 가상 네트워크 내 에서만 포털 편집기를 사용 하거나 Azure Portal에 액세스 하는 데 사용 하는 컴퓨터의 IP를 허용 목록 수 있습니다. 그러나 모든 컴퓨터에서 **플랫폼 기능** 탭의 모든 기능에 액세스할 수 있습니다.

자세히 알아보려면 [Azure App Service 고정 액세스 제한](../app-service/app-service-ip-restrictions.md)을 참조 하세요.

## <a name="private-site-access"></a>프라이빗 사이트 액세스

개인 사이트 액세스는 Azure virtual network 내에서와 같은 개인 네트워크 에서만 앱에 액세스할 수 있도록 하는 것을 말합니다. 
* 개인 사이트 액세스는 **서비스 끝점이** 구성 될 때 [프리미엄](./functions-premium-plan.md) 및 [App Service 계획](functions-scale.md#app-service-plan) 에서 사용할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) 을 참조 하세요.
    * 서비스 끝점을 사용 하는 경우 가상 네트워크 통합이 구성 된 경우에도 함수에서 인터넷에 대 한 전체 아웃 바운드 액세스를 계속 유지 합니다.
* 개인 사이트 액세스는 ILB (내부 부하 분산 장치)로 구성 된 App Service Environment 에서도 사용할 수 있습니다. 자세한 내용은 [App Service Environment를 사용 하 여 내부 부하 분산 장치 만들기 및 사용](../app-service/environment/create-ilb-ase.md)을 참조 하세요.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

가상 네트워크 통합을 사용 하면 함수 앱이 가상 네트워크 내의 리소스에 액세스할 수 있습니다. 이 기능은 프리미엄 요금제와 App Service 계획에서 모두 사용할 수 있습니다. 앱이 App Service Environment에 있는 경우 이미 가상 네트워크에 있으며 가상 네트워크 통합을 사용 하 여 동일한 가상 네트워크의 리소스에 연결할 필요가 없습니다.

가상 네트워크 통합을 사용 하 여 가상 네트워크에서 실행 되는 데이터베이스 및 웹 서비스에 대 한 앱 액세스를 설정할 수 있습니다. 가상 네트워크 통합을 사용 하면 VM의 응용 프로그램에 대 한 공용 끝점을 노출할 필요가 없습니다. 대신 개인, 비 인터넷 라우팅 가능 주소를 사용할 수 있습니다.

가상 네트워크 통합 기능에는 두 가지 형태가 있습니다.

1. 지역 가상 네트워크 통합을 사용 하면 동일한 지역의 가상 네트워크와 통합할 수 있습니다. 이 기능의 형태에는 동일한 지역의 가상 네트워크에 있는 서브넷이 필요 합니다. 이 기능은 아직 미리 보기 상태 이지만 아래에 설명 된 몇 가지 주의 사항으로 Windows 앱 프로덕션 워크 로드에 대해 지원 됩니다.
2. 게이트웨이는 가상 네트워크 통합을 사용 하 여 원격 지역의 가상 네트워크 또는 클래식 가상 네트워크와 통합할 수 있습니다. 이 버전의 기능을 사용 하려면 VNet에 Virtual Network 게이트웨이를 배포 해야 합니다. 이는 지점 및 사이트 간 VPN 기반 기능이 며 Windows 앱 에서만 지원 됩니다.

앱은 한 번에 한 가지 형식의 VNet 통합 기능만 사용할 수 있습니다. 그런 다음 사용 해야 하는 기능에 대 한 질문입니다. 여러 항목에 대해 사용할 수 있습니다. 분명히 차이점는 다음과 같습니다.

| 문제점  | 솔루션 | 
|----------|----------|
| 동일한 지역에서 RFC 1918 주소 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)에 도달 하려고 합니다. | 지역 VNet 통합 |
| 클래식 VNet 또는 다른 지역의 VNet에서 리소스에 도달 하려고 합니다. | 게이트웨이 필요 VNet 통합 |
| Express 경로에서 RFC 1918 끝점에 도달 하려고 합니다. | 지역 VNet 통합 |
| 서비스 끝점에서 리소스에 연결 하려고 합니다. | 지역 VNet 통합 |

두 기능 모두 Express 경로에서 비 RFC 1918 주소에 도달할 수 있습니다. 이렇게 하려면 ASE를 지금 사용 해야 합니다.

지역 VNet 통합을 사용 하는 경우 VNet을 온-프레미스에 연결 하거나 서비스 끝점을 구성 하지 않습니다. 별도의 네트워킹 구성입니다. 지역 VNet 통합을 사용 하면 응용 프로그램에서 이러한 연결 형식에 대 한 호출을 수행할 수 있습니다.

사용 된 버전에 관계 없이 VNet 통합은 가상 네트워크의 리소스에 대 한 함수 앱 액세스를 제공 하지만 가상 네트워크에서 함수 앱에 대 한 개인 사이트 액세스를 부여 하지 않습니다. 프라이빗 사이트 액세스는 Azure Virtual Network와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로의 아웃 바운드 호출을 수행 하는 경우에만 사용할 수 있습니다. 

VNet 통합 기능에는 다음이 적용됩니다.

* Standard, Premium 또는 PremiumV2 App Service 계획이 필요 합니다.
* TCP 및 UDP를 지원 합니다.
* App Service 앱 및 함수 앱에서 작동

VNet 통합에서 지원하지 않는 다음 몇 가지 항목이 있습니다.

* 드라이브 탑재
* AD 통합 
* NetBios

기능의 가상 네트워크 통합은 App Service 웹 앱과 공유 인프라를 사용 합니다. 두 가지 유형의 가상 네트워크 통합에 대 한 자세한 내용은 다음을 참조 하세요.
* [지역 VNET 통합](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [게이트웨이 필요 VNet 통합](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

가상 네트워크 통합을 사용 하는 방법에 대 한 자세한 내용은 [Azure 가상 네트워크와 함수 앱 통합](functions-create-vnet.md)을 참조 하세요.

## <a name="virtual-network-triggers-non-http"></a>가상 네트워크 트리거 (비 HTTP)

현재 가상 네트워크 내에서 HTTP 이외의 함수 트리거를 사용할 수 있으려면 App Service 계획 또는 App Service Environment에서 함수 앱을 실행 해야 합니다.

예를 들어 가상 네트워크의 트래픽만 허용 하도록 Azure Cosmos DB를 구성 하는 경우 해당 가상 네트워크와 가상 네트워크 통합을 사용 하 여 앱 서비스 계획에 함수 앱을 배포 하 여 Azure Cosmos DB 트리거를 구성 해야 합니다. 해당 리소스에서. 미리 보기 상태에서 VNET 통합을 구성 하면 프리미엄 계획에서 해당 Azure Cosmos DB 리소스를 트리거할 수 없습니다.

[HTTP가 아닌 모든 트리거에 대해이 목록을](./functions-triggers-bindings.md#supported-bindings) 확인 하 여 지원 되는 항목을 두 번 확인 합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결](../service-bus-relay/relay-hybrid-connections-protocol.md) 는 다른 네트워크의 응용 프로그램 리소스에 액세스 하는 데 사용할 수 있는 Azure Relay의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 응용 프로그램에 액세스 하는 데 사용할 수 없습니다. 하이브리드 연결는 [App Service 계획과](functions-scale.md#app-service-plan) [App Service Environment](../app-service/environment/intro.md)에서 실행 되는 함수에 사용할 수 있습니다.

Azure Functions에서 사용 되는 것 처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합과 관련이 있습니다. 즉, TCP 수신 대기 포트에 액세스 하는 한, 하이브리드 연결의 끝점은 모든 운영 체제 및 응용 프로그램에 있을 수 있습니다. 하이브리드 연결 기능은 응용 프로그램 프로토콜이 무엇 인지 또는 사용자가 액세스 하는 것을 알 수 없습니다. 단지 네트워크 액세스를 제공 합니다.

자세한 내용은 App Service 계획의 함수를 지 원하는 [하이브리드 연결에 대 한 App Service 설명서](../app-service/app-service-hybrid-connections.md)를 참조 하세요.

## <a name="outbound-ip-restrictions"></a>아웃 바운드 IP 제한

아웃 바운드 IP 제한은 App Service Environment에 배포 된 함수에만 사용할 수 있습니다. App Service Environment 배포 되는 가상 네트워크에 대 한 아웃 바운드 제한을 구성할 수 있습니다.

Premium 요금제 또는 App Service 계획의 함수 앱을 가상 네트워크와 통합 하는 경우에도 앱에서 인터넷에 대 한 아웃 바운드 호출을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
네트워킹 및 Azure Functions에 대 한 자세한 내용은 다음을 확인 하세요. 

* [Virtual network 통합 시작에 대 한 자습서를 따르세요.](./functions-create-vnet.md)
* [기능 네트워킹 FAQ를 참조 하십시오.](./functions-networking-faq.md)
* [App Service/함수와의 가상 네트워크 통합에 대해 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure의 가상 네트워크에 대 한 자세한 정보](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능을 사용 하 고 App Service 환경 제어](../app-service/environment/intro.md)
* [하이브리드 연결를 사용 하 여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)
