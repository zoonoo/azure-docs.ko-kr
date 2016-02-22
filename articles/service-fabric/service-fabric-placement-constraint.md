<properties
   pageTitle="서비스 패브릭 클러스터 오케스트레이션 배치 제약 조건 | Microsoft Azure"
   description="서비스 패브릭에서 배치 제약 조건의 개념 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/03/2016"
   ms.author="masnider"/>

# 배치 제약 조건 개요

Azure 서비스 패브릭을 사용하면 개발자가 특정 조건을 충족하는 노드로 서비스 복제본의 배치를 제한할 수 있습니다. 이러한 조건은 적절한 서비스-컨텍스트별 값으로 평가되는 부울 식을 통해 표현됩니다.


## 기능
배치 제약 조건을 사용하면 다음을 수행할 수 있습니다.

- 노드에 대해 NodeProperties를 정의하여 서로 다른 유형의 서비스를 서로 다른 노드로 제한합니다.

- 서비스의 기본 복제본과 보조 복제본에 적용할 제약 조건을 대상으로 지정하거나 기본 복제본에만 적용할 제약 조건을 대상으로 지정합니다.


## 주요 개념
NodeProperty - 문자열을 값에 매핑하는 사용자 정의 또는 시스템 정의 맵으로, 각 노드(NodeName)에 따라 달라질 수 있습니다.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

자세한 내용은 [응용 프로그램 시나리오](service-fabric-application-scenarios.md)를 참조하세요.

<!---HONumber=AcomDC_0211_2016-->