---
title: Azure Functions 네트워킹 옵션
description: Azure Functions에서 사용 가능한 모든 네트워킹 옵션의 개요
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 10d7daa6da45c56e20c622fcbca9ee288e737dab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358168"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 네트워킹 옵션

이 문서에서 Azure Functions 호스팅 옵션의 네트워킹 기능을 사용할 수 있는 도구 모음을 설명 합니다. 다음과 같은 네트워킹 옵션의 모든 인터넷 라우팅 가능 주소를 사용 하지 않고 리소스에 액세스 하거나 함수 앱에 대 한 인터넷 액세스를 제한 하는 일부 기능을 제공 합니다. 모든 호스팅 모델은 서로 다른 수준의 네트워크 격리를 사용할 수 있고 올바른 템플릿을 선택 하면 네트워크 격리 요구 사항을 충족 합니다.

여러 가지 방법으로 함수 앱을 호스트할 수 있습니다.

* 다양 한 수준의 vnet 대 VNET 연결 및 확장 옵션을 사용 하 여 다중 테 넌 트 인프라에서 실행 되는 계획 옵션 집합이 있습니다.
    1. 소비 계획을 로드에 대 한 응답에서 동적으로 확장 하 고 최소한의 네트워크 격리 옵션을 제공 합니다.
    1. 보다 포괄적인 네트워크 격리를 제공 하는 동안 동적으로 조정 하는 프리미엄 계획
    1. App Service 계획 고정 규모로 작동 하며 프리미엄 요금제에 유사한 네트워크 격리를 제공 합니다.
* 또한 함수에는 ASE App Service Environment () 함수 VNet에 배포 하 고 전체 네트워크 제어 및 격리를 제공 하는 실행할 수 있습니다.

## <a name="networking-feature-matrix"></a>네트워킹 기능 매트릭스

|                |[소비 계획](functions-scale.md#consumption-plan)|⚠ [프리미엄 요금제](functions-scale.md##premium-plan-public-preview)|[App Service 플랜](functions-scale.md#app-service-plan)|[App Service 환경](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**인바운드 IP 제한**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**VNET 통합**](#vnet-integration)|❌No|⚠ 예|✅Yes|✅Yes|
|[**미리 보기 VNET 통합 (Express 경로 및 서비스 끝점)**](#preview-vnet-integration)|❌No|⚠ 예|⚠ 예|✅Yes|
|[**하이브리드 연결**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**개인 사이트 액세스**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ 프로덕션용 미리 보기 기능

## <a name="inbound-ip-restrictions"></a>인바운드 IP 제한

IP 제한을 사용하여 앱 액세스가 허용되는 IP 주소의 허용/거부 목록을 우선 순위대로 정의할 수 있습니다. 허용 목록에는 IPv4 및 IPv6 주소가 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 모두 거부가 표시됩니다. IP 제한 기능을 모든 함수 호스팅 옵션을 사용 하 여 작동 합니다.

> [!NOTE]
> Azure portal 편집기를 사용 하 여 할 일 하려면 포털을 실행 중인 함수 앱에 직접 액세스할 수 있어야 하 고 포털에 액세스 하려면 사용 중인 장치에는 해당 IP 허용 목록에 있어야 합니다. 네트워크 제한 사항이 사용 하 여 액세스할 수 있습니다에서 모든 기능을 **플랫폼 기능** 탭 합니다.

[자세히 알아보기](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>VNET 통합

VNET 통합에는 함수 앱을 VNET 내부의 리소스에 액세스할 수 있습니다. VNET 통합은 Premium 계획 및 App Service 계획에서 사용할 수 있습니다. App Service Environment의 앱 인 경우 다음 VNet에 이미 하 고 동일한 VNet에서 리소스를 연결할 VNet 통합 기능을 사용 하 여 필요 하지 않습니다.

VNet 통합은 가상 네트워크의 리소스에 함수 앱 액세스를 부여 하지만 부여 하지 [개인 사이트 액세스](#private-site-access) 가상 네트워크에서 함수 앱으로 합니다.

VNet 통합은 종종 VNet에서 실행되는 데이터베이스 및 웹 서비스에 앱에서 액세스할 수 있도록 하는 데 사용됩니다. VNet 통합을 사용하면 VM의 애플리케이션에 대한 공개 엔드포인트를 노출할 필요가 없지만 인터넷에서 사용할 수 없고 라우팅할 있는 개인 주소를 대신 사용할 수 있습니다.

VNET 통합의 일반 공급 버전 함수 앱을 가상 네트워크에 연결 하는 VPN gateway에 의존 합니다. App service 계획에서 호스트 하는 함수에 제공 됩니다. 이 기능을 구성 하는 방법에 알아보려면 참조를 [동일한 기능에 대 한 App Service 문서](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration)합니다.

### <a name="preview-vnet-integration"></a>VNET 통합 미리 보기

미리 보기에는 VNet 통합 기능의 새 버전이 있습니다. 이는 지점 및 사이트 간 VPN을 사용하지 않으며, ExpressRoute 또는 서비스 엔드 포인트를 통한 리소스 액세스를 지원합니다. 이 기능은 프리미엄 요금제에 PremiumV2를 확장 하는 App Service 계획에 사용할 수 있습니다.

현재 미리 보기에 있는 VNet 통합의 새 버전에는 다음과 같은 이점을 제공 합니다.

* 게이트웨이는 새 VNet 통합 기능을 사용하는 데 필요하지 않습니다.
* VNet에 연결된 ExpressRoute와의 통합을 제외하고는 추가 구성 없이 ExpressRoute 연결을 통해 리소스에 액세스할 수 있습니다.
* 보안 리소스 함수를 실행 하는 서비스 끝점을 사용할 수 있습니다. 이렇게 하려면 VNet 통합에 사용되는 서브넷에서 서비스 엔드포인트를 사용하도록 설정합니다.
* 새 VNet 통합 기능을 사용 하 여 리소스를 보호 하는 서비스 끝점을 사용 하는 트리거를 구성할 수 없습니다. 
* 함수 앱과 VNet은 모두 동일한 지역에 있어야 합니다.
* 새 기능을 사용하려면 Resource Manager VNet에서 사용되지 않는 서브넷이 필요합니다.
* 미리 보기 중인 동안에 프로덕션 워크 로드 VNet 통합의 새 버전에서 지원 되지 않습니다.
* 경로 테이블과 글로벌 피어링은 새 VNet 통합에서 아직 사용할 수 없습니다.
* 각 잠재적인 함수 앱 인스턴스에 대 한 하나의 주소가 사용 됩니다. 할당 후 서브넷 크기를 변경할 수 없으므로, 최대 배율 크기를 쉽게 지원할 수 있는 서브넷을 사용 합니다. 예를 들어, 80 인스턴스로 확장할 수 있는 프리미엄 계획을 지원 하려면 것이 좋습니다는 `/25` 126 호스트 주소를 제공 하는 서브넷입니다.

미리 보기 VNET 통합을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure Virtual Network를 사용 하 여 함수 앱을 통합](functions-create-vnet.md)합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

[하이브리드 연결](../service-bus-relay/relay-hybrid-connections-protocol.md) 다른 네트워크의 응용 프로그램 리소스에 액세스할 수 있는 Azure Relay의 기능입니다. 이를 통해 앱에서 애플리케이션 엔드포인트에 액세스할 수 있습니다. 응용 프로그램 액세스에 사용할 수 없습니다. 하이브리드 연결이에서 실행 되는 함수에 사용할 수는 [App Service 계획](functions-scale.md#app-service-plan) 와 [App Service Environment](../app-service/environment/intro.md)합니다.

함수에서 사용 하는, 것 처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결 합니다. 즉, TCP 수신 대기 포트에 액세스하는 한, 하이브리드 연결 엔드포인트는 모든 운영 체제 및 모든 애플리케이션에 있을 수 있습니다. 하이브리드 연결 기능은 애플리케이션 프로토콜이 무엇인지 또는 사용자가 무엇에 액세스하고 있는지 인식하거나 상관하지 않습니다. 단지 네트워크 액세스를 제공합니다.

자세한 내용은 참조는 [하이브리드 연결에 대 한 App Service 설명서](../app-service/app-service-hybrid-connections.md), 함수 및 웹 앱을 모두 지 합니다.

## <a name="private-site-access"></a>개인 사이트 액세스

개인 사이트 액세스는 Azure Virtual Network와 같은 개인 네트워크에서만 앱에 액세스할 수 있도록 합니다. 개인 사이트 액세스는 ILB(내부 부하 분산 장치)로 ASE가 구성된 상태에서만 사용할 수 있습니다. ILB ASE를 사용 하 여 세부 정보를 참조 하세요 [만들기 및 ILB ASE를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

다른 호스팅 옵션에 대 한 VNET 리소스에 액세스 하는 방법은 여러 가지 이지만 ASE는 VNET을 통해 발생 하는 함수에 대 한 트리거를 허용 하는 유일한 방법은 있습니다.
