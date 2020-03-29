---
title: Azure Functions의 네트워킹에 대해 자주 묻는 질문
description: Azure Functions를 사용하여 네트워킹을 위한 가장 일반적인 질문 및 시나리오 중 일부에 대한 답변입니다.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409537"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions의 네트워킹에 대해 자주 묻는 질문

이 문서에서는 Azure Functions의 네트워킹에 대해 자주 묻는 질문을 나열합니다. 보다 포괄적인 개요는 [기능 네트워킹 옵션을](functions-networking-options.md)참조하십시오.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>함수에서 정적 IP를 설정하려면 어떻게 해야 합니까?

앱 서비스 환경에서 함수를 배포하는 것이 현재 함수에 대한 정적 인바운드 및 아웃바운드 IP를 가질 수 있는 유일한 방법입니다. 앱 서비스 환경 사용에 대한 자세한 내용은 앱 [서비스 환경이](../app-service/environment/create-ilb-ase.md)있는 내부 부하 분산 기 만들기 및 사용 설명으로 시작합니다.

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>내 기능에 대한 인터넷 액세스를 제한하려면 어떻게 해야 합니까?

다음과 같은 몇 가지 방법으로 인터넷 액세스를 제한할 수 있습니다.

* [IP 제한](../app-service/app-service-ip-restrictions.md): IP 범위별로 기능 앱으로의 인바운드 트래픽을 제한합니다.
    * IP 제한에 따라 특정 가상 네트워크의 인바운드 트래픽만 허용하도록 함수를 제한하는 [서비스 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md)구성할 수도 있습니다.
* 모든 HTTP 트리거 제거. 일부 응용 프로그램의 경우 HTTP 트리거를 피하고 다른 이벤트 소스를 사용하여 함수를 트리거하는 것으로 충분합니다.

Azure 포털 편집기에서 실행 중인 함수에 직접 액세스해야 합니다. Azure 포털을 통해 코드를 변경하려면 포털을 탐색하는 데 사용하는 장치가 있어야 IP가 목록에 추가됩니다. 그러나 플랫폼 기능 탭에서 네트워크 제한이 있는 모든 것을 사용할 수 있습니다.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>함수 앱을 가상 네트워크로 제한하려면 어떻게 해야 합니까?

[서비스 끝점을](./functions-networking-options.md#private-site-access)사용하여 함수 앱의 **인바운드** 트래픽을 가상 네트워크로 제한할 수 있습니다. 이 구성은 여전히 함수 앱이 인터넷에 아웃바운드 호출을 할 수 있도록 합니다.

모든 트래픽이 가상 네트워크를 통해 흐르도록 기능을 완전히 제한하는 유일한 방법은 내부적으로 부하 분산앱 서비스 환경을 사용하는 것입니다. 이 옵션은 가상 네트워크 내부의 전용 인프라에 사이트를 배포하고 가상 네트워크를 통해 모든 트리거와 트래픽을 보냅니다. 

앱 서비스 환경 사용에 대한 자세한 내용은 앱 [서비스 환경이](../app-service/environment/create-ilb-ase.md)있는 내부 부하 분산 기 만들기 및 사용 설명으로 시작합니다.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>함수 앱에서 가상 네트워크의 리소스에 액세스하는 방법은 무엇입니까?

가상 네트워크 통합을 사용하여 실행 중인 함수에서 가상 네트워크의 리소스에 액세스할 수 있습니다. 자세한 내용은 [가상 네트워크 통합](functions-networking-options.md#virtual-network-integration)을 참조하십시오.

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>서비스 끝점으로 보호되는 리소스에 액세스하려면 어떻게 해야 합니까?

가상 네트워크 통합을 사용하면 실행 중인 함수에서 서비스 엔드포인트 보안 리소스에 액세스할 수 있습니다. 자세한 내용은 [가상 네트워크 통합](functions-networking-options.md#virtual-network-integration)을 참조하십시오.

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>가상 네트워크의 리소스에서 함수를 트리거하면 어떻게 해야 합니까?

[서비스 끝점을](./functions-networking-options.md#private-site-access)사용하여 가상 네트워크에서 HTTP 트리거를 호출하도록 허용할 수 있습니다. 

또한 함수 앱을 프리미엄 계획, 앱 서비스 계획 또는 앱 서비스 환경에 배포하여 가상 네트워크의 다른 모든 리소스에서 함수를 트리거할 수도 있습니다. 자세한 내용은 [비 HTTP 가상 네트워크 트리거를](./functions-networking-options.md#virtual-network-triggers-non-http) 참조하십시오.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>가상 네트워크에 함수 앱을 배포하는 방법은 무엇입니까?

앱 서비스 환경에 배포하는 것이 가상 네트워크 내부에 있는 함수 앱을 만들 수 있는 유일한 방법입니다. 앱 서비스 환경과 함께 내부 로드 밸런서 사용에 대한 자세한 내용은 [앱 서비스 환경의 내부 로드 밸런서 만들기 및 내부 로드 밸런서 사용으로](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)시작합니다.

가상 네트워크 리소스에 대한 단방향 액세스만 필요하거나 덜 포괄적인 네트워크 격리가 필요한 시나리오는 [Functions 네트워킹 개요를](functions-networking-options.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

네트워킹 및 기능에 대해 자세히 알아보려면 다음을 수행하십시오. 

* [가상 네트워크 통합 시작에 대한 자습서를 따르십시오.](./functions-create-vnet.md)
* [Azure Functions의 네트워킹 옵션에 대해 자세히 알아보기](./functions-networking-options.md)
* [앱 서비스 및 기능과의 가상 네트워크 통합에 대해 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure의 가상 네트워크에 대해 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [앱 서비스 환경으로 더 많은 네트워킹 기능 및 제어 지원](../app-service/environment/intro.md)
