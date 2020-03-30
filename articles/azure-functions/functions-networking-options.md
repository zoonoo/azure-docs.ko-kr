---
title: Azure Functions 네트워킹 옵션
description: Azure Functions에서 사용할 수 있는 모든 네트워킹 옵션에 대한 개요입니다.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276713"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 네트워킹 옵션

이 문서에서는 Azure Functions에 대한 호스팅 옵션에서 사용할 수 있는 네트워킹 기능에 대해 설명합니다. 다음 의 모든 네트워킹 옵션을 사용하면 인터넷 라우팅 주소를 사용하지 않고 리소스에 액세스하거나 함수 앱에 대한 인터넷 액세스를 제한할 수 있습니다.

호스팅 모델에는 다양한 수준의 네트워크 격리가 사용 가능합니다. 올바른 것을 선택하면 네트워크 격리 요구 사항을 충족하는 데 도움이 됩니다.

다음과 같은 몇 가지 방법으로 함수 앱을 호스팅할 수 있습니다.

* 다양한 수준의 가상 네트워크 연결 및 확장 옵션을 갖춘 다중 테넌트 인프라에서 실행되는 계획 옵션 집합이 있습니다.
    * 부하에 따라 동적으로 확장되고 최소한의 네트워크 격리 옵션을 제공하는 [소비 계획입니다.](functions-scale.md#consumption-plan)
    * 더 포괄적인 네트워크 격리를 제공하면서 동적으로 확장되는 [프리미엄 플랜입니다.](functions-scale.md#premium-plan)
    * 고정 된 규모로 작동 하 고 프리미엄 계획으로 유사한 네트워크 격리를 제공 하는 Azure [앱 서비스 계획.](functions-scale.md#app-service-plan)
* [앱 서비스 환경에서](../app-service/environment/intro.md)기능을 실행할 수 있습니다. 이 방법은 기능을 가상 네트워크에 배포하고 전체 네트워크 제어 및 격리를 제공합니다.

## <a name="matrix-of-networking-features"></a>네트워킹 기능의 매트릭스

|                |[소비 계획](functions-scale.md#consumption-plan)|[프리미엄 플랜](functions-scale.md#premium-plan)|[앱 서비스 계획](functions-scale.md#app-service-plan)|[앱 서비스 환경](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[개인 사이트 액세스를 & 인바운드 IP 제한](#inbound-ip-restrictions)|✅예|✅예|✅예|✅예|
|[가상 네트워크 통합](#virtual-network-integration)|❌아니요|✅예(지역)|✅예(지역 및 게이트웨이)|✅예|
|[가상 네트워크 트리거(HTTP가 아닌 경우)](#virtual-network-triggers-non-http)|❌아니요| ✅예 |✅예|✅예|
|[하이브리드](#hybrid-connections) 연결(Windows만)|❌아니요|✅예|✅예|✅예|
|[아웃바운드 IP 제한](#outbound-ip-restrictions)|❌아니요| ✅예|✅예|✅예|

## <a name="inbound-ip-restrictions"></a>인바운드 IP 제한

IP 제한을 사용하여 앱에 대한 액세스가 허용되거나 거부된 우선 순위의 IP 주소 목록을 정의할 수 있습니다. 목록에는 IPv4 및 IPv6 주소가 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 암시적 "모두 거부"가 목록 끝에 존재합니다. IP 제한은 모든 기능 호스팅 옵션에서 작동합니다.

> [!NOTE]
> 네트워크 제한이 있는 경우 가상 네트워크 내에서만 포털 편집기를 사용하거나 사용 중이던 컴퓨터의 IP 주소를 사용하여 안전 받는 사람 목록에 Azure 포털에 액세스할 수 있습니다. 그러나 모든 컴퓨터에서 **플랫폼 기능** 탭의 모든 기능에 계속 액세스할 수 있습니다.

자세한 내용은 [Azure App Service 정적 액세스 제한을](../app-service/app-service-ip-restrictions.md)참조하십시오.

## <a name="private-site-access"></a>프라이빗 사이트 액세스

개인 사이트 액세스란 Azure 가상 네트워크와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 하는 것을 말합니다.

* 개인 사이트 액세스는 서비스 끝점이 구성될 때 [프리미엄,](./functions-premium-plan.md) [소비](functions-scale.md#consumption-plan)및 [앱 서비스](functions-scale.md#app-service-plan) 계획에서 사용할 수 있습니다.
    * 서비스 끝점은 **플랫폼 기능** > **네트워킹** > **구성 액세스 제한** > **추가 규칙에서**앱별로 구성할 수 있습니다. 이제 가상 네트워크를 규칙 유형으로 선택할 수 있습니다.
    * 자세한 내용은 [가상 네트워크 서비스 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md)참조하십시오.
    * 서비스 끝점을 사용하면 가상 네트워크 통합이 구성된 경우에도 인터넷에 대한 전체 아웃바운드 액세스 권한을 계속 유지할 수 있습니다.
* 개인 사이트 액세스는 ILB(내부 로드 밸런서)로 구성된 앱 서비스 환경 내에서도 사용할 수 있습니다. 자세한 내용은 [앱 서비스 환경의 내부 부하 분산 검사기 만들기 및 사용을](../app-service/environment/create-ilb-ase.md)참조하십시오.

개인 사이트 액세스를 설정하는 방법을 알아보려면 [Azure Functions 개인 사이트 액세스 설정](functions-create-private-site-access.md)을 참조하십시오.

## <a name="virtual-network-integration"></a>가상 네트워크 통합

가상 네트워크 통합을 사용하면 함수 앱이 가상 네트워크 내부의 리소스에 액세스할 수 있습니다. Azure Functions는 두 가지 종류의 가상 네트워크 통합을 지원합니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions의 가상 네트워크 통합은 앱 서비스 웹 앱과 공유 인프라를 사용합니다. 두 가지 유형의 가상 네트워크 통합에 대해 자세히 알아보려면 다음을 참조하십시오.

* [지역 가상 네트워크 통합](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [게이트웨이 필수 가상 네트워크 통합](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

가상 네트워크 통합을 설정하는 방법에 대해 알아보려면 [Azure 가상 네트워크와 함수 앱 통합을](functions-create-vnet.md)참조하십시오.

## <a name="regional-virtual-network-integration"></a>지역 가상 네트워크 통합

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>서비스 엔드포인트 보안 리소스에 연결

더 높은 수준의 보안을 제공하기 위해 서비스 끝점을 사용하여 여러 Azure 서비스를 가상 네트워크로 제한할 수 있습니다. 그런 다음 리소스에 액세스하려면 함수 앱을 해당 가상 네트워크와 통합해야 합니다. 이 구성은 가상 네트워크 통합을 지원하는 모든 계획에서 지원됩니다.

[가상 네트워크 서비스 엔드포인트에 대해 자세히 알아보세요.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>저장소 계정을 가상 네트워크로 제한

함수 앱을 만들 때 Blob, Queue 및 테이블 저장소를 지원하는 범용 Azure Storage 계정을 만들거나 링크해야 합니다. 현재 이 계정에서가상 네트워크 제한을 사용할 수 없습니다. 함수 앱에 사용 중이던 저장소 계정에 가상 네트워크 서비스 끝점을 구성하면 앱이 중단됩니다.

[저장소 계정 요구 사항에 대해 자세히 알아보세요.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>키 볼트 참조 사용 

키 볼트 참조를 사용하면 코드를 변경하지 않고도 Azure Functions 응용 프로그램에서 Azure Key Vault의 암호를 사용할 수 있습니다. Azure Key Vault는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다.

키 볼트가 서비스 끝점으로 보호되는 경우 키 [볼트 참조가](../app-service/app-service-key-vault-references.md) 작동하지 않습니다. 가상 네트워크 통합을 사용하여 키 볼트에 연결하려면 응용 프로그램 코드에서 키 자격 증명 모음을 호출해야 합니다.

## <a name="virtual-network-triggers-non-http"></a>가상 네트워크 트리거(HTTP가 아닌 경우)

현재 다음 두 가지 방법 중 하나로 가상 네트워크 내에서 비 HTTP 트리거 함수를 사용할 수 있습니다. 
+ 프리미엄 계획에서 함수 앱을 실행하고 가상 네트워크 트리거 지원을 활성화합니다.
+ 앱 서비스 계획 또는 앱 서비스 환경에서 함수 앱을 실행합니다.

### <a name="premium-plan-with-virtual-network-triggers"></a>가상 네트워크 트리거가 있는 프리미엄 요금제

Premium 요금제에서 실행할 때 비 HTTP 트리거 함수를 가상 네트워크 내에서 실행되는 서비스에 연결할 수 있습니다. 이렇게 하려면 함수 앱에 대한 가상 네트워크 트리거 지원을 사용하도록 설정해야 합니다. **가상 네트워크 트리거 지원** 설정은 기능 앱 설정 아래의 Azure [포털에서](https://portal.azure.com) 찾을 수 **있습니다.**

![브네토글](media/functions-networking-options/virtual-network-trigger-toggle.png)

다음 Azure CLI 명령을 사용하여 가상 네트워크 트리거를 활성화할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

가상 네트워크 트리거는 버전 2.x 이상에서 지원되며 함수 런타임 이상입니다. 다음 비 HTTP 트리거 형식이 지원됩니다.

| 내선 번호 | 최소 버전 |
|-----------|---------| 
|[마이크로소프트.Azure.웹잡스.확장.스토리지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 이상 |
|[마이크로소프트.Azure.웹잡스.확장.이벤트 허브](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 이상|
|[마이크로소프트.Azure.웹잡스.확장.서비스버스](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 이상|
|[마이크로소프트.Azure.웹잡스.익스텐션.코스모스DB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 이상|
|[마이크로소프트.Azure.WebJobs.확장.내구성 작업](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 이상|

> [!IMPORTANT]
> 가상 네트워크 트리거 지원을 사용하도록 설정하면 위의 트리거 유형만 응용 프로그램과 동적으로 확장됩니다. 위에 나열되지 않은 트리거는 계속 사용할 수 있지만 미리 준비된 인스턴스 수를 초과하여 확장되지는 않습니다. [트리거 및 바인딩전체](./functions-triggers-bindings.md#supported-bindings) 트리거 목록을 참조하십시오.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>가상 네트워크 트리거가 있는 앱 서비스 계획 및 앱 서비스 환경

함수 앱이 앱 서비스 계획 또는 앱 서비스 환경에서 실행되는 경우 비 HTTP 트리거 함수를 사용할 수 있습니다. 함수가 올바르게 트리거되려면 트리거 연결에 정의된 리소스에 액세스할 수 있는 가상 네트워크에 연결되어 있어야 합니다. 

예를 들어 Azure Cosmos DB가 가상 네트워크에서만 트래픽을 허용하도록 구성한다고 가정합니다. 이 경우 해당 가상 네트워크와 가상 네트워크 통합을 제공하는 앱 서비스 계획에 함수 앱을 배포해야 합니다. 이렇게 하면 해당 Azure Cosmos DB 리소스에 의해 함수를 트리거할 수 있습니다. 

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결은](../service-bus-relay/relay-hybrid-connections-protocol.md) 다른 네트워크의 응용 프로그램 리소스에 액세스하는 데 사용할 수 있는 Azure 릴레이의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 응용 프로그램에 액세스하는 데 사용할 수 없습니다. 하이브리드 연결은 소비 계획을 제외한 모든 Windows에서 실행되는 함수에 사용할 수 있습니다.

Azure Functions에서 사용되는 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합과 상관 관계가 있습니다. 즉, 하이브리드 연결의 끝점은 TCP 수신 대기 포트에 액세스하는 한 모든 운영 체제 및 모든 응용 프로그램에 있을 수 있습니다. 하이브리드 연결 기능은 응용 프로그램 프로토콜이 무엇인지 또는 액세스하는 내용을 알지 못하거나 신경 쓰지 않습니다. 그것은 단지 네트워크 액세스를 제공합니다.

자세한 내용은 하이브리드 [연결에 대한 앱 서비스 설명서를](../app-service/app-service-hybrid-connections.md)참조하십시오. 이러한 동일한 구성 단계는 Azure 함수를 지원합니다.

>[!IMPORTANT]
> 하이브리드 연결은 Windows 계획에서만 지원됩니다. 리눅스는 지원되지 않습니다

## <a name="outbound-ip-restrictions"></a>아웃바운드 IP 제한

아웃바운드 IP 제한은 프리미엄 요금제, 앱 서비스 요금제 또는 앱 서비스 환경에서 사용할 수 있습니다. 앱 서비스 환경이 배포된 가상 네트워크에 대한 아웃바운드 제한을 구성할 수 있습니다.

프리미엄 요금제 또는 앱 서비스 계획에 기능 앱을 가상 네트워크와 통합해도 앱은 기본적으로 인터넷에 아웃바운드 호출을 할 수 있습니다. 응용 프로그램 설정을 `WEBSITE_VNET_ROUTE_ALL=1`추가하면 모든 아웃바운드 트래픽이 가상 네트워크로 전송되어 네트워크 보안 그룹 규칙을 사용하여 트래픽을 제한할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계

네트워킹 및 Azure 기능에 대해 자세히 알아보려면 다음을 수행하십시오.

* [가상 네트워크 통합 시작에 대한 자습서를 따르십시오.](./functions-create-vnet.md)
* [기능 네트워킹 FAQ 읽기](./functions-networking-faq.md)
* [앱 서비스/기능과의 가상 네트워크 통합에 대해 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure의 가상 네트워크에 대해 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [앱 서비스 환경으로 더 많은 네트워킹 기능 및 제어 지원](../app-service/environment/intro.md)
* [하이브리드 연결을 사용하여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)
