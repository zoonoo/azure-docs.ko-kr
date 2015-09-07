<properties
   pageTitle="서비스 패브릭 서비스의 가용성"
	description="서비스에 대한 오류 검색, 장애 조치, 복구를 설명합니다."
	services="service-fabric"
	documentationCenter=".net"
	authors="appi101"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/26/2015"
	ms.author="aprameyr"/>

# 서비스 패브릭 서비스의 가용성
서비스 패브릭 서비스는 상태 저장 또는 상태 비저장이 모두 될 수 있습니다. 이 문서에서는 서비스 패브릭이 오류 발생 시 서비스의 가용성을 유지하는 방법에 대한 개요를 설명합니다.

## 서비스 패브릭 상태 비저장 서비스의 가용성
상태 비저장 서비스는 [로컬 지속 상태](service-fabric-concepts-state.md)가 없는 응용 프로그램 서비스입니다.

상태 비저장 서비스를 만들려면 클러스터에서 실행되어야 하는 상태 비저장 서비스의 인스턴스 수를 정의해야 합니다. 이는 클러스터에서 인스턴스화되는 응용 프로그램 논리의 복사본 수입니다. 인스턴스 수를 늘리는 것은 상태 비저장 서비스를 확장하는 데 바람직한 방법입니다.

상태 비저장 서비스의 어느 인스턴스에서든 오류가 감지되면 새 인스턴스가 클러스터의 다른 적합한 일부 노드에 만들어집니다.

## 서비스 패브릭 상태 저장 서비스의 가용성
상태 저장 서비스에는 이와 관련된 일부 상태가 있습니다. 서비스 패브릭에서 상태 저장 서비스는 복제본 세트로 모델링됩니다. 각 복제본은 상태의 복사본을 갖는 서비스 코드의 인스턴스입니다. 읽기 및 쓰기 작업은 하나의 복제본(Primary라 불림)에서 수행됩니다. 쓰기 작업에 의한 상태 변경은 다른 여러 복제본(Active Secondary라 불림)으로 *복제*됩니다. Primary와 Active Secondary 복제본의 조합은 서비스의 복제본 세트입니다.

읽기 및 쓰기 요청을 서비스하는 Primary는 오로지 하나뿐입니다. 여러 개의 Active Secondary 복제본이 있을 수 있습니다. Active Secondary 복제본의 수는 구성 가능하며 복제본의 수가 많을수록 더 많은 수의 소프트웨어 및 하드웨어 오류를 동시에 허용할 수 있습니다.

오류 발생 시(Primary 복제본이 중지하는 경우), 서비스 패브릭은 Active Secondary 복제본 중 하나를 새 Primary 복제본으로 만듭니다. 이 Active Secondary 복제본은 이미 상태의 업데이트된 버전을 가지므로(*복제*를 통해) 읽기 및 쓰기 작업의 프로세싱을 계속할 수 있습니다.

복제본이 Primary 또는 Active Secondary가 되는 이 개념은 복제본 역할로 알려져 있습니다.

### 복제본 역할
복제본의 역할은 해당 복제본에 의해 관리되는 상태의 수명 주기를 관리하는 데 사용됩니다. Primary 역할의 복제본은 읽기 요청을 서비스합니다. 또한 해당 상태를 업데이트하여 쓰기 요청을 서비스하고 변경 사항을 해당 복제본 세트의 Active Secondary에 복제합니다. Active Secondary는 Primary 복제본이 복제되고 상태 보기를 업데이트하는 상태 변경 수신을 담당합니다.

>[AZURE.NOTE][Reliable Actors 프레임워크](service-fabric-reliable-actors-introduction.md)와 같은 더 높은 수준의 프로그래밍 모델은 개발자의 복제본 역할의 개념을 추상화합니다.

## 다음 단계

서비스 패브릭 개념에 대한 자세한 내용은 다음을 참조하십시오.

- [서비스 패브릭 서비스의 확장성](service-fabric-concepts-scalability.md)

- [서비스 패브릭 서비스의 분할](service-fabric-concepts-partitioning.md)

- [상태 정의 및 관리](service-fabric-concepts-state.md)
 

<!---HONumber=August15_HO9-->