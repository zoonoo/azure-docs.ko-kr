---
title: "Azure Service Fabric 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내부 | Microsoft Docs"
description: "신뢰할 수 있는 컬렉션 개념 및 Azure Service Fabric의 디자인에 대해 자세히 알아봅니다."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내부
이 문서에서는 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 내부를 탐구하여 핵심 구성 요소가 백그라운드에서 어떻게 작동하는지 살펴봅니다.

> [!NOTE]
> 이 문서는 진행 중입니다. 이 문서에 의견을 추가하여 자세히 알고 싶은 항목을 알려주세요.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>로컬 지속성 모델: 로그 및 검사점
신뢰할 수 있는 상태 관리자와 신뢰할 수 있는 컬렉션은 로그 및 검사점이라고 하는 지속성 모델을 따릅니다.
이 모델에서 각 상태 변경은 먼저 디스크에 기록된 후 메모리에 적용됩니다.
전체 상태 자체는 가끔씩만 유지됩니다.(즉, 검사점).
이를 통해 성능 향상을 위해 델타가 디스크에 대한 순차 추가 전용 쓰기로 변환된다는 이점이 제공됩니다.

로그 및 검사점 모델을 보다 잘 이해하기 위해 먼저 무한 디스크 시나리오를 살펴보겠습니다.
신뢰할 수 있는 상태 관리자는 복제되기 전에 모든 작업을 기록합니다.
로깅하면 신뢰할 수 있는 컬렉션이 해당 작업만 메모리에 적용할 수 있습니다.
로그가 유지되므로 복제본이 실패하고 다시 시작해야 하는 경우에도 신뢰할 수 있는 상태 관리자에게 복제본이 손실한 모든 작업을 재생할 충분한 정보가 로그에 있습니다.
디스크가 무한이므로 로그 레코드를 제거할 필요가 없고 신뢰할 수 있는 컬렉션만 메모리 내 상태를 관리해야 합니다.

이제 한정된 디스크 시나리오를 살펴보겠습니다.
로그 레코드가 누적되면 신뢰할 수 있는 상태 관리자에게 디스크 공간이 부족한 경우가 생깁니다.
이런 일이 발생하기 전에 신뢰할 수 있는 상태 관리자가 로그를 잘라서 최신 레코드를 위한 공간을 확보해야 합니다.
신뢰할 수 있는 상태 관리자는 디스크에 대해 신뢰할 수 있는 컬렉션에 메모리 내 상태의 검사점을 설정하도록 요청합니다.
이 시점에서 신뢰할 수 있는 컬렉션은 메모리 내 상태를 유지합니다.
신뢰할 수 있는 컬렉션이 검사점을 완료하면 신뢰할 수 있는 상태 관리자가 로그를 잘라서 디스크 공간을 확보할 수 있습니다.
복제본을 다시 시작해야 하는 경우 신뢰할 수 있는 컬렉션이 검사점이 설정된 상태를 복구하고, 신뢰할 수 있는 상태 관리자가 마지막 검사점 이후 발생한 모든 상태 변경 내용을 다시 재생합니다.

검사점에 다른 값이 추가되면 일반적인 시나리오에서 복구 시간이 개선됩니다. 로그에는 마지막 검사점 이후 발생한 모든 작업이 포함됩니다.
따라서 신뢰할 수 있는 사전에서 지정된 행에 대한 여러 값과 같이 항목의 여러 버전이 포함될 수 있습니다.
반대로, 신뢰할 수 있는 컬렉션 검사점은 키에 대한 각 값의 최신 버전에만 해당합니다.

## <a name="next-steps"></a>다음 단계
* [트랜잭션 및 잠금](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

