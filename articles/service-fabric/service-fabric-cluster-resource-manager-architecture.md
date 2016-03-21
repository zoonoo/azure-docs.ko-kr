<properties
   pageTitle="리소스 관리자 아키텍처 | Microsoft Azure"
   description="서비스 패브릭 클러스터 리소스 관리자의 아키텍처 개요"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# 클러스터 리소스 관리자 아키텍처 개요
클러스터의 리소스를 관리하려면 클러스터 리소스 관리자에게 일부 정보가 있어야 합니다. 현재 존재하는 서비스와 서비스를 소비하는 리소스의 현재(또는 기본) 양을 알아야 합니다. 클러스터에서 노드의 실제 용량과 클러스터에서 특정 노드의 전체 용량과 나머지 용량에 대해 사용할 수 있는 리소스 양을 알아야 합니다. 주어진 서비스의 리소스 소모량은 지남에 따라 변한다는 사실과 실제로 서비스는 일반적으로 여러 리소스를 관리한다는 사실에 대해 다루어야 합니다. 여러 가지 다양한 서비스에는 메모리 및 디스크 사용량 등 여러 유형의 서비스에서 흔히 사용되는 "실제" 리소스와 특정 서비스만이 관리하는 리소스가 있을 수 있습니다.

보다 복잡한 개념은 클러스터의 소유자와 연산자는 종종 서비스 작성자와 다를 수 있거나 같은 사람이 다른 모자를 쓸 수 있다는 점입니다. 가령 어떤 리소스가 필요한지와 여러 구성 요소가 이상적으로 배포되는 방식에 대해 다소 알고 있지만, 수행해야 할 다른 작업이 있고 다른 도구가 필요한 프로덕션 환경에서 해당 서비스에 대한 라이브 사이트 문제를 처리하는 사람으로서 서비스를 개발하는 경우입니다. 또한 클러스터나 서비스가 정적으로 구성되지 않으면, 클러스터의 노드 수가 증가 및 감소할 수 있고, 다양한 크기의 노드가 이동할 수 있으며, 서비스가 리소스 할당을 변경하고, 서비스가 생성되고 제거될 수 있습니다. 업그레이드 또는 기타 관리 작업은 클러스터에서 실행될 수 있으며, 또 언제든지 작업이 실패할 수도 있습니다.

리소스 관리자는 특정 서비스의 요구 사항뿐만 아니라 전체 클러스터 자체에 대해 많은 것을 알아야 합니다. 이를 수행하기 위해, 서비스 패브릭에는 로컬 리소스 사용 정보를 집계하기 위해 개별 노드에서 실행되는 리소스 관리자와 서비스 및 클러스터에 대한 정보를 집계하고 클러스터 및 서비스의 원하는 상태 구성에 따른 변경 사항에 반응하는 중앙 집중식 내결함성 리소스 관리자 서비스 등의 두 에이전트가 있습니다. 내결함성은 서비스 상태를 몇 개의 복제본(일반적으로 7개)에 복제하는 서비스와 동일한 메커니즘으로 정확히 구현됩니다.

![리소스 분산 아키텍처][Image1]

예로서, 이 다이어그램(그림 1)을 살펴보겠습니다. 런타임 도중 수많은 변경 사항이 발생할 수 있습니다. 예를 들어, 리소스 서비스 소비량, 일부 서비스 실패, 일부 노드의 클러스터 가입 또는 탈퇴 등 몇 가지 변경 사항이 있다고 합시다. 특정 노드에 대한 모든 변경 사항은 집계되어 중앙 리소스 관리자 서비스(1, 2)로 정기적으로 전송되고, 여기서 다시 집계되고 분석되고 저장됩니다. 중앙 서비스는 몇 초마다 모든 변경 사항을 보고 필요한 작업이 있는지 판단합니다(3). 예를 들어 노드가 클러스터에 추가되고 비워지고 일부 서비스는 해당 노드로 이동되었는지 알 수 있습니다. 또한 특정 노드의 오버로드되었거나 특정 서비스가 실패하여(또는 삭제됨) 다른 노드의 리소스를 확보되었는지 확인할 수도 있습니다.

이제 다음 다이어그램(그림 2)을 보고 이 예에서 무슨 일이 발생하는지 확인해보겠습니다. 리소스 관리자는 변경이 필요한지 판단한다고 가정해봅시다. 이는 다른 시스템 서비스(특히 장애 조치 관리자)와 함께 필요한 변경을 수행합니다. 그런 다음 변경 요청은 적절한 노드(4)로 전송됩니다. 이 경우 리소스 관리자는 노드 5가 오버로드되어 서비스 B가 N5에서 N4로 이동하기로 결정했다고 가정합니다. 재구성(5)이 마무리되면 클러스터는 다음과 같이 됩니다.

![리소스 분산 아키텍처][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
- [클러스터 리소스 관리자 아키텍처에 대해 알아보기](service-fabric-cluster-resource-manager-architecture.md)
- [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0309_2016-->