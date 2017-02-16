---
title: "Azure 마이크로 서비스에서 상태 정의 및 관리| Microsoft Docs"
description: "서비스 패브릭에서 서비스 상태를 정의하고 관리하는 방법"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: d03bd6a4c317da67a4e6d0e8cdb0cbd3f07d5a1f


---
# <a name="service-state"></a>서비스 상태
**서비스 상태**는 서비스가 작동하기 위해 필요한 데이터를 나타냅니다. 서비스가 작업을 수행하기 위해 읽고 쓰는 데이터 구조 및 변수를 포함합니다.

계산기 서비스를 예로 들어보겠습니다. 이 서비스는 두 개의 숫자를 받아 그 합계를 반환합니다. 이는 연결된 데이터가 없는 순수한 상태 비저장 서비스입니다.

이제 합계를 계산하는 것에 더해 계산한 마지막 합계를 반환하는 메서드를 갖춘 것 외에는 동일한 계산기를 가정합니다. 이 서비스는 이제 읽을 상태(새 합계를 계산한 경우)와 쓸 상태(마지막으로 계산한 합계를 반환하는 경우)를 포함하는 상태 저장 서비스입니다.

Azure 서비스 패브릭에서 첫 번째 서비스는 상태 비저장 서비스라고 합니다. 두 번째 서비스는 상태 저장 서비스라고 합니다.

## <a name="storing-service-state"></a>서비스 상태 저장
상태는 외장화될 수도 있고 상태를 조작하는 코드와 함께 위치할 수도 있습니다. 상태의 외장화는 일반적으로 외장 데이터베이스 또는 저장소를 사용하여 이루어집니다. 계산기 예제에서는 현재 결과가 테이블에 저장되는 SQL 데이터베이스가 될 수 있습니다. 합계를 계산하라는 모든 요청은 이 행에 대한 업데이트를 수행합니다.

상태는 이 코드를 조작하는 코드와 함께 위치할 수도 있습니다. Service Fabric의 상태 저장 서비스는 이 모델을 사용하여 빌드될 수 있습니다. 서비스 패브릭은 이 상태의 높은 가용성과 오류 발생 시 내결함성을 보장하는 인프라를 제공합니다.

## <a name="next-steps"></a>다음 단계
Service Fabric 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

* [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
* [서비스 패브릭 서비스의 확장성](service-fabric-concepts-scalability.md)
* [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO4-->


