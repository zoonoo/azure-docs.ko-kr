---
title: Azure 스프링 클라우드 지오 재해 복구 | 마이크로 소프트 문서
description: 지역 정전으로부터 스프링 클라우드 애플리케이션을 보호하는 방법 알아보기
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279148"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 스프링 클라우드 재해 복구

이 문서에서는 Azure Spring Cloud 응용 프로그램이 가동 중지 시간을 경험하지 않도록 보호하는 데 사용할 수 있는 몇 가지 전략에 대해 설명합니다.  모든 지역 또는 데이터 센터는 지역 재해로 인한 가동 중지 시간이 발생할 수 있지만 신중한 계획을 세면으로 계획하면 고객에게 미치는 영향을 줄일 수 있습니다.

## <a name="plan-your-application-deployment"></a>응용 프로그램 배포 계획

Azure 스프링 클라우드 응용 프로그램은 특정 지역에서 실행됩니다.  Azure는 전 세계 여러 지역에서 작동합니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함하는 지역 내의 영역입니다.  각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. Azure는 지역 쌍 간에 플랫폼 업데이트(계획된 유지 관리)를 직렬화하여 각 쌍의 한 지역만 한 번에 업데이트되도록 합니다. 여러 지역에 영향을 주는 중단의 경우 각 쌍에서 하나 이상의 지역에 복구 우선 순위가 지정됩니다.

재해로부터 고가용성과 보호를 보장하려면 Spring Cloud 애플리케이션을 여러 지역에 배포해야 합니다.  Azure는 지역 쌍에 대한 Spring Cloud 배포를 계획할 수 있도록 [페어링된 지역](../best-practices-availability-paired-regions.md) 목록을 제공합니다.  마이크로 서비스 아키텍처를 디자인할 때 지역 가용성, Azure 쌍을 이루는 지역 및 서비스 가용성이라는 세 가지 핵심 요소를 고려하는 것이 좋습니다.

*  지역 가용성: 네트워크 지연 및 전송 시간을 최소화하려면 사용자와 가까운 지리적 영역을 선택합니다.
*  Azure 페어링 지역: 선택한 지리적 영역 내에서 쌍을 이루는 지역을 선택하여 조정된 플랫폼 업데이트 및 필요한 경우 우선 순위가 지정된 복구 노력을 보장합니다.
*  서비스 가용성: 페어링된 지역이 핫/핫/ 온열 또는 핫/콜드를 실행할지 여부를 결정합니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

[Azure 트래픽 관리자는](../traffic-manager/traffic-manager-overview.md) DNS 기반 트래픽 로드 균형 조정을 제공하고 여러 지역에 네트워크 트래픽을 배포할 수 있습니다.  Azure 트래픽 관리자를 사용하여 고객을 가장 가까운 Azure Spring Cloud 서비스 인스턴스로 안내합니다.  최상의 성능과 중복성을 위해 Azure Spring Cloud 서비스로 보내기 전에 Azure 트래픽 관리자를 통해 모든 응용 프로그램 트래픽을 직접 전송합니다.

여러 지역에 Azure Spring Cloud 응용 프로그램이 있는 경우 Azure 트래픽 관리자를 사용하여 각 지역의 응용 프로그램에 대한 트래픽 흐름을 제어합니다.  서비스 IP를 사용하여 각 서비스에 대한 Azure 트래픽 관리자 끝점을 정의합니다. 고객은 Azure Spring 클라우드 서비스를 가리키는 Azure 트래픽 관리자 DNS 이름에 연결해야 합니다.  Azure 트래픽 관리자 로드는 정의된 끝점 전체에서 트래픽의 균형을 조정합니다.  재해가 데이터 센터에 발생하면 Azure 트래픽 관리자는 해당 지역에서 해당 쌍으로 트래픽을 지시하여 서비스 연속성을 보장합니다.