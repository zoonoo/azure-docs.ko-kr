---
title: "Traffic Manager란 | Microsoft Docs"
description: "이 문서는 Traffic Manager란 무엇이며 응용 프로그램에 대한 올바른 트래픽 라우팅 선택인지를 이해하도록 도움을 줍니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 79c14e713fd58d3f69f5978e550a78e8e84d286d

---

# <a name="overview-of-traffic-manager"></a>Traffic Manager 개요

Microsoft Azure Traffic Manager를 사용하면 다양한 데이터 센터에서 서비스 끝점에 대한 사용자 트래픽의 배포를 제어할 수 있습니다. Traffic Manager에서 지원되는 서비스 끝점은 Azure VM, Web Apps 및 클라우드 서비스를 포함합니다. 또한 외부, Azure가 아닌 끝점으로 Traffic Manager를 사용할 수 있습니다.

Traffic Manager는 DNS(Domain Name System)를 사용하여 클라이언트 요청을 [트래픽 라우팅 메서드](traffic-manager-routing-methods.md) 및 끝점의 상태를 기반으로 가장 적절한 끝점으로 리디렉션합니다. Traffic Manager는 다양한 응용 프로그램 요구 사항, 끝점 상태 [모니터링](traffic-manager-monitoring.md) 및 자동 장애 조치에 잘 맞는 트래픽 라우팅 메서드를 제공합니다. Traffic Manager는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.

## <a name="traffic-manager-benefits"></a>Traffic Manager 이점

Traffic Manager은 다음과 같은 작업에 도움이 될 수 있습니다.

* **중요 응용 프로그램의 가용성 향상**

    Traffic Manager는 끝점을 모니터링하여 끝점이 중단될 경우 자동 장애 조치를 제공함으로써 응용 프로그램에 대한 높은 가용성을 제공합니다.

* **고성능 응용 프로그램의 응답성 향상**

    Azure를 통해 전 세계에 있는 데이터 센터에서 클라우드 서비스나 웹 사이트를 실행할 수 있습니다. Traffic Manager는 클라이언트에서 가장 낮은 네트워크 대기 시간으로 트래픽을 끝점에 연결하여 응용 프로그램의 응답성을 향상시킵니다.

* **가동 중지 시간 없이 서비스 유지 관리 수행**

    가동 중지 시간 없이 응용 프로그램에 계획된 유지 관리 작업을 수행할 수 있습니다. Traffic Manager는 유지 관리가 진행 중인 동안 트래픽을 대체 끝점으로 전달합니다.

* **온-프레미스와 클라우드 기반 응용 프로그램의 결합**

    Traffic Manager는 "클라우드로 버스트", "클라우드로 마이그레이션" 및 "클라우드로 장애 조치(Failover)" 시나리오를 포함한 하이브리드 클라우드 및 온-프레미스 배포에 사용할 수 있도록 외부, Azure가 아닌 끝점을 지원합니다.

* **복잡한 대규모 배포를 위한 트래픽 분산**

    [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용하여 트래픽 라우팅 방법을 더 크고 복잡한 배포에 대한 요구 사항을 지원하는 복잡하고 유연한 규칙을 만들도록 결합할 수 있습니다.

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 작동 방식](traffic-manager-how-traffic-manager-works.md)에 대해 자세히 알아봅니다.
* [Traffic Manager 끝점 모니터링](traffic-manager-monitoring.md)을 사용하여 고가용성 응용 프로그램을 개발하는 방법을 알아봅니다.
* Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
* [Traffic Manager 프로필을 만듭니다](traffic-manager-manage-profiles.md).



<!--HONumber=Nov16_HO3-->


