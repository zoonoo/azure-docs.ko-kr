---
title: Azure 스프링 클라우드 지역 재해 복구 | Microsoft Docs
description: 지역 가동 중단 으로부터 스프링 클라우드 응용 프로그램을 보호 하는 방법을 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: e8f32f574a4ff7be0cc3cc7915b8203b53824c63
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792329"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 스프링 클라우드 재해 복구

이 문서에서는 가동 중지 시간이 발생 하지 않도록 Azure 스프링 클라우드 응용 프로그램을 보호 하는 데 사용할 수 있는 몇 가지 전략을 설명 합니다.  지역 또는 데이터 센터에서 지역 재해로 인 한 가동 중지 시간이 발생할 수 있지만 신중한 계획은 고객에 대 한 영향을 완화할 수 있습니다.

## <a name="plan-your-application-deployment"></a>응용 프로그램 배포 계획

Azure 스프링 클라우드 응용 프로그램은 특정 지역에서 실행 됩니다.  Azure는 전 세계 여러 지역에서 작동합니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함 하는 지리 내의 영역입니다.  각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. Azure는 지역 쌍에서 플랫폼 업데이트 (계획 된 유지 관리)를 직렬화 하 여 한 번에 각 쌍의 한 지역이 업데이트 되도록 합니다. 여러 지역에 영향을 주는 중단의 경우 각 쌍에서 하나 이상의 지역에 복구 우선 순위가 지정됩니다.

재해 로부터 고가용성 및 보호를 보장 하려면 스프링 클라우드 응용 프로그램을 여러 지역에 배포 해야 합니다.  Azure는 지역 쌍에 대 한 스프링 클라우드 배포를 계획할 수 있도록 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md) 목록을 제공 합니다.  마이크로 서비스 아키텍처를 설계할 때 지역 가용성, Azure 쌍을 이루는 지역 및 서비스 가용성의 세 가지 주요 요소를 고려 하는 것이 좋습니다.

*  지역 가용성: 네트워크 지연 및 전송 시간을 최소화 하기 위해 사용자에 게 가까운 지역 지역을 선택 합니다.
*  Azure 쌍을 이루는 지역: 선택한 지역 내에서 페어링된 지역을 선택 하 여 조정 된 플랫폼 업데이트를 보장 하 고 필요한 경우 복구 작업의 우선 순위를 지정 합니다.
*  서비스 가용성: 페어링된 지역이 핫/핫, 핫/웜 또는 핫/콜드을 실행 해야 하는지 여부를 결정 합니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 는 DNS 기반 트래픽 부하 분산을 제공 하 고 여러 지역에 걸쳐 네트워크 트래픽을 분산할 수 있습니다.  Azure Traffic Manager을 사용 하 여 고객에 게 가장 가까운 Azure 스프링 클라우드 서비스 인스턴스로 고객을 안내 합니다.  최상의 성능 및 중복성을 위해 azure 스프링 클라우드 서비스로 보내기 전에 Azure Traffic Manager를 통해 모든 응용 프로그램 트래픽을 전달 합니다.

Azure 스프링 클라우드 응용 프로그램이 여러 지역에 있는 경우 Azure Traffic Manager를 사용 하 여 각 지역의 응용 프로그램에 대 한 트래픽 흐름을 제어할 수 있습니다.  서비스 IP를 사용 하 여 각 서비스에 대 한 Azure Traffic Manager 끝점을 정의 합니다. 고객은 Azure 스프링 클라우드 서비스를 가리키는 Azure Traffic Manager DNS 이름에 연결 해야 합니다.  Azure Traffic Manager 정의 된 끝점에서 트래픽 부하를 분산 합니다.  재해가 발생 한 데이터 센터에서 Azure Traffic Manager는 해당 지역에서 해당 쌍으로 트래픽을 전달 하 여 서비스 연속성을 보장 합니다.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Azure 스프링 클라우드 용 Azure Traffic Manager 만들기

1. 서로 다른 두 지역에 Azure 스프링 클라우드를 만듭니다.
두 개의 서로 다른 지역 (미국 동부 및 유럽 서부)에 배포 된 두 개의 Azure 스프링 클라우드 서비스 인스턴스가 필요 합니다. Azure Portal를 사용 하 여 기존 Azure 스프링 클라우드 응용 프로그램을 시작 하 여 두 서비스 인스턴스를 만듭니다. 각는 트래픽에 대 한 기본 및 장애 조치 (failover) 끝점 역할을 합니다. 

**두 서비스 인스턴스 정보:**

| 서비스 이름 | 위치 | 애플리케이션 |
|--|--|--|
| 서비스-샘플-a | 미국 동부 | 게이트웨이/인증-서비스/계정-서비스 |
| 서비스-샘플-b | 서유럽 | 게이트웨이/인증-서비스/계정-서비스 |

2. 서비스에 대 한 사용자 지정 도메인 설정 사용자 지정 도메인 [문서](spring-cloud-tutorial-custom-domain.md) 를 참조 하 여 이러한 두 개의 기존 서비스 인스턴스에 대 한 사용자 지정 도메인을 설정 합니다. 성공적으로 설정 되 면 두 서비스 인스턴스가 사용자 지정 도메인에 바인딩합니다. bcdr-test.contoso.com

3. Traffic manager 및 두 개의 끝점 만들기: [Azure Portal를 사용 하 여 Traffic Manager 프로필을 만듭니다](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile).

Traffic manager 프로필은 다음과 같습니다.
* Traffic Manager DNS 이름:http://asc-bcdr.trafficmanager.net
* 끝점 프로필: 

| 프로필 | Type | 대상 | 우선 순위 | 사용자 지정 헤더 설정 |
|--|--|--|--|--|
| 끝점 프로필 | 외부 끝점 | service-sample-a.asc-test.net | 1 | 호스트: bcdr-test.contoso.com |
| 끝점 B 프로필 | 외부 끝점 | service-sample-b.asc-test.net | 2 | 호스트: bcdr-test.contoso.com |

4. DNS 영역: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net에서 CNAME 레코드를 만듭니다. 

5. 이제 환경이 완전 하 게 설정 됩니다. 고객은 다음을 통해 앱에 액세스할 수 있어야 합니다. bcdr-test.contoso.com