---
title: Azure Service Fabric 이벤트 | Microsoft Docs
description: Azure Service Fabric 클러스터를 모니터링할 수 있도록 기본적으로 제공되는 Service Fabric 이벤트에 대해 자세히 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 936a47593b9db6e4989c30b2df37dfd82c286c59
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290521"
---
# <a name="service-fabric-events"></a>Service Fabric 이벤트 

Service Fabric 플랫폼은 클러스터 내에서 발생하는 주요 운영 활동에 대한 여러 가지 구조화된 이벤트를 기록합니다. 클러스터 업그레이드부터 복제본 배치 결정까지 이 범위에 포함됩니다. Service Fabric에서 노출하는 각 이벤트는 클러스터의 다음 엔터티 중 하나로 매핑됩니다.
* 프로비전
* 애플리케이션
* 서비스
* 파티션
* 복제본 
* 컨테이너

플랫폼에서 노출하는 이벤트 전체 목록은 [Service Fabric 이벤트 목록](service-fabric-diagnostics-event-generation-operational.md)을 참조하세요.

다음은 클러스터에 대한 이벤트가 표시되는 시나리오의 몇 가지 예입니다. 
* 노드 수명 주기 이벤트: 노드가 증가, 감소, 스케일 인/아웃, 다시 시작, 활성화/비활성화되면 이벤트가 노출되어 발생한 일을 보여주고, 컴퓨터 자체에 문제가 있는지 또는 노드 상태를 수정하기 위해 SF를 통해 호출된 API가 있는지 식별하는 데 도움을 줍니다.
* 클러스터 업그레이드: 클러스터가 업그레이드되면(SF 버전 또는 구성 변경) 업그레이드가 시작되고, 각 업그레이드 도메인을 실행하고, 완료(또는 롤백)되는 것을 볼 수 있습니다. 
* 애플리케이션 업그레이드: 클러스터 업그레이드와 마찬가지로, 업그레이드가 실행되는 포괄적인 이벤트 세트가 있습니다. 이러한 이벤트는 업그레이드가 예약된 시간, 현재 업그레이드 상태, 전체 이벤트 시퀀스를 이해하는 데 도움이 될 수 있습니다. 이는 어떤 업그레이드가 롤아웃되었는지 또는 롤백이 트리거되었는지 여부를 검토하는 데 유용합니다.
* 애플리케이션/서비스 배포/삭제: 생성 또는 삭제되는 각 애플리케이션, 서비스 및 컨테이너에 대한 이벤트가 있으며, 스케일 인 또는 스케일 아웃(예: 복제본 수 증가)할 때 유용합니다.
* 파티션 이동(재구성): 상태 저장 파티션이 재구성(복제본 세트가 변경)될 때마다 이벤트가 기록됩니다. 이는 파티션 복제본 세트가 얼마나 자주 변경되는지 또는 장애 조치(failover)되는지 알아보고 싶거나 특정 시점에 어떤 노드가 주 복제본을 실행하는지 추적하려는 경우에 유용합니다.
* Chaos 이벤트: Service Fabric의 [Chaos](service-fabric-controlled-chaos.md) 서비스를 사용하는 경우 서비스가 시작 또는 중지될 때마다 또는 시스템에 오류를 삽입할 때마다 이벤트를 볼 수 있습니다.
* 상태 이벤트: Service Fabric은 경고 또는 오류 상태 보고서가 작성되거나 엔터티가 정상 상태로 돌아가거나 상태 보고서가 만료될 때마다 상태 이벤트를 노출합니다. 이러한 이벤트는 엔터티의 과거 상태를 추적하는 데 매우 유용합니다. 

## <a name="how-to-access-events"></a>이벤트에 액세스하는 방법

Service Fabric 이벤트에 액세스할 수 있는 몇 가지 방법이 있습니다.
* 이벤트는 ETW/Windows 이벤트 로그와 같은 표준 채널을 통해 기록되고 Log Analytics와 같이 이러한 이벤트를 지원하는 모니터링 도구에서 시각화할 수 있습니다. 기본적으로 포털에서 만든 클러스터에는 진단이 켜고 있고, 이벤트를 Azure Table 스토리지로 보내는 Windows Azure 진단 에이전트가 있지만 여전히 Log Analytics 리소스에 통합해야 합니다. 추가 로그나 성능 카운터 및 [Log Analytics 통합](service-fabric-diagnostics-event-analysis-oms.md)를 확인하려면 진단 구성을 수정하도록 [Azure 진단 에이전트](service-fabric-diagnostics-event-aggregation-wad.md)를 구성하는 방법에 대한 자세한 내용을 읽어보세요.
* 클러스터를 직접 또는 Service Fabric 클러스터 라이브러리를 통해 쿼리할 수 있는 EventStore 서비스의 Rest API. [EventStore API를 통한 클러스터 이벤트 쿼리](service-fabric-diagnostics-eventstore-query.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 클러스터 모니터링에 대한 자세한 내용 - [클러스터 및 플랫폼 모니터링](service-fabric-diagnostics-event-generation-infra.md).
* EventStore 서비스에 대해 자세히 알아보기 - [EventStore 서비스 개요](service-fabric-diagnostics-eventstore.md)
