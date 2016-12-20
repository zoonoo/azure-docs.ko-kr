---
title: "Reliable Services 고급 사용법 | Microsoft Docs"
description: "서비스에 유연성을 더해 주는 서비스 패브릭의 Reliable Services 고급 사용법에 대해 알아봅니다."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a0180074df84b1e8eba27066787559d8d2c80c50


---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>신뢰할 수 있는 서비스 프로그래밍 모델 고급 사용법
Azure 서비스 패브릭은 신뢰할 수 있는 상태 비저장 및 상태 저장 서비스의 작성과 관리를 단순화합니다. 이 가이드는 서비스에 대한 더 많은 제어와 유연성을 확보할 수 있는 Reliable Services의 고급 사용법에 대해 설명합니다. 이 가이드를 읽기 전에 [신뢰할 수 있는 서비스 프로그래밍 모델](service-fabric-reliable-services-introduction.md)에 대해 숙지하세요.

상태 저장 서비스와 상태 비저장 서비스 모두 사용자 코드에 대한 두 개의 기본 진입점이 있습니다.

* `RunAsync` 는 서비스 코드에 대한 범용 진입점입니다.
* `CreateServiceReplicaListeners` 및 `CreateServiceInstanceListeners`는 클라이언트 요청에 대한 통신 수신기를 여는 데 사용됩니다.

대부분의 서비스에는 이 두 진입점만 있으면 됩니다. 드문 경우이지만 서비스의 수명 주기에 대한 제어를 강화해야 하는 경우 추가 수명 주기 이벤트를 사용할 수 있습니다.

## <a name="stateless-service-instance-lifecycle"></a>상태 비저장 서비스 인스턴스 수명 주기
상태 비저장 서비스의 수명 주기는 매우 간단합니다. 상태 비저장 서비스는 열리거나, 닫히거나, 중단될 수만 있습니다. `RunAsync` 는 서비스 인스턴스가 열리면 실행되고 서비스 인스턴스가 닫히거나 중단되면 취소됩니다. 

`RunAsync` 는 거의 모든 경우에 충분하지만 상태 비저장 서비스의 열기, 닫기 및 중단 이벤트는에 다음을 사용할 수도 있습니다.

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
   상태 비저장 서비스 인스턴스를 사용하려고 할 때 OnOpenAsync가 호출됩니다. 이때 확장된 서비스 초기화 작업을 시작할 수 있습니다.
* `Task OnCloseAsync(CancellationToken)`
   상태 비저장 서비스 인스턴스가 정상적으로 종료되려고 할 때 OnCloseAsync가 호출됩니다. 이는 서비스의 코드를 업그레이드할 때, 로드 균형 조정으로 인해 서비스 인스턴스가 이동할 때 또는 일시적인 오류가 감지되었을 때 발생할 수 있습니다. OnCloseAsync를 사용하여 모든 리소스를 안전하게 닫거나, 백그라운드 프로세싱을 중지하거나, 외부 상태 저장을 완료하거나, 기존 연결을 종료할 수 있습니다.
* `void OnAbort()`
   상태 비저장 서비스 인스턴스가 정상적으로 종료되려고 할 때 OnAbort가 호출됩니다. 이는 일반적으로 노드에서 영구 오류가 감지되거나, 내부 오류로 인해 서비스 패브릭에서 서비스 인스턴스 수명 주기를 안정적으로 관리할 수 없을 때 호출됩니다.

## <a name="stateful-service-replica-lifecycle"></a>상태 저장 서비스 복제본 수명 주기
상태 저장 서비스 복제본의 수명 주기는 상태 비저장 서비스 인스턴스보다 훨씬 더 복잡합니다. 열기, 닫기 및 중단 이벤트 외에 상태 저장 서비스 복제본은 수명 동안 역할이 변경됩니다. 상태 저장 서비스 복제본의 역할이 변경된 경우 `OnChangeRoleAsync` 이벤트가 트리거됩니다.

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
   상태 저장 서비스 복제본이 역할을 변경할 경우(예: 주 또는 보조) OnChangeRoleAsync가 호출됩니다. 주 복제본에는 쓰기 상태가 지정됩니다(신뢰할 수 있는 컬렉션을 만들고 쓰도록 허용). 보조 복제본에는 읽기 상태가 지정됩니다(신뢰할 수 있는 기존 컬렉션에서 읽기만 가능). 상태 저장 서비스에서 대부분의 작업은 주 복제본에서 수행됩니다. 보조 복제본은 읽기 전용 유효성 검사, 보고서 생성, 데이터 마이닝 또는 다른 읽기 전용 작업을 수행할 수 있습니다.

상태 저장 서비스에서는 주 복제본만 상태에 대한 쓰기 권한을 가지므로 일반적으로 서비스에서 실제 작업을 수행하는 경우에 적용됩니다. 상태 저장 서비스의 `RunAsync` 메서드는 상태 저장 서비스 복제본이 주 복제본인 경우에만 실행됩니다. 주 복제본의 역할이 변경되거나 닫기 또는 중단 이벤트 중에는 `RunAsync` 메서드가 취소됩니다. 

`OnChangeRoleAsync` 이벤트를 사용하면 복제본의 역할에 따라 작업을 수행하거나 역할 변경에 대한 응답으로 작업을 수행할 수 있습니다.

또한 상태 저장 서비스는 상태 비저장 서비스와 동일한 4개의 수명 주기 이벤트(의미 체계 및 사용 사례가 동일함)를 제공합니다.

* `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
* `Task OnCloseAsync(CancellationToken)`
* `void OnAbort()`

## <a name="next-steps"></a>다음 단계
서비스 패브릭과 관련된 고급 항목을 보려면 다음 문서를 참조하세요.

* [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md)
* [서비스 패브릭 상태 소개](service-fabric-health-introduction.md)
* [문제 해결을 위해 시스템 상태 보고서 사용](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [서비스 패브릭 클러스터 리소스 관리자를 사용한 서비스 구성](service-fabric-cluster-resource-manager-configure-services.md)




<!--HONumber=Nov16_HO3-->


