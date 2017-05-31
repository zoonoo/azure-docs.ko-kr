---
title: "Azure의 Service Fabric 개요 | Microsoft Docs"
description: "응용 프로그램이 여러 마이크로 서비스로 구성되어 확장성과 복원력을 제공하는 서비스 패브릭의 개요입니다. Service Fabric은 확장 가능하고 안정적이며 관리하기 쉬운 클라우드 응용 프로그램을 빌드하는 데 사용되는 분산 시스템 플랫폼입니다."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: mfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 81dae4c35fead96af8a8a2823952a0bd7f2e537e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="overview-of-azure-service-fabric"></a>Azure Service Fabric의 개요
Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 또한 서비스 패브릭은 클라우드 응용 프로그램 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다. Service Fabric은 엔터프라이즈급 계층 1 클라우드 규모의 응용 프로그램을 빌드 및 관리하기 위한 차세대 미들웨어 플랫폼을 나타냅니다.

이 짧은 Channel9 비디오에서는 Service Fabric 및 마이크로 서비스를 소개합니다.<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

이 긴 Microsoft Virtual Academy 비디오에서는 Service Fabric의 핵심 개념을 설명합니다.<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-overview/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="applications-composed-of-microservices"></a>마이크로 서비스로 구성된 응용 프로그램
Service Fabric을 사용하면 컴퓨터의 공유 풀(클러스터라고 함)에서 매우 높은 밀도로 실행되는 마이크로 서비스로 구성된 확장성 있고 신뢰할 수 있는 응용 프로그램을 빌드하고 관리할 수 있습니다. 분산되고 확장 가능한 상태 비저장 및 상태 저장 마이크로 서비스를 빌드하는 정교한 런타임을 제공합니다. 또한 배포된 응용 프로그램을 프로비전, 배포, 모니터링, 업그레이드/패치 및 삭제하는 포괄적인 응용 프로그램 관리 기능을 제공합니다.

마이크로 서비스 접근 방식이 중요한 이유는 무엇일까요? 두 가지 주요 이유가 있습니다.

* 응용 프로그램의 요구 사항에 따라 여러 부분을 확장할 수 있습니다.
* 개발 팀은 변경 내용을 보다 민첩하게 롤아웃할 수 있으므로 고객에게 보다 빠르고 빈번하게 기능을 제공할 수 있습니다.

Service Fabric은 Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, 비즈니스용 Skype, 기타 많은 주요 Azure 서비스 등 오늘날의 여러 Microsoft 서비스를 지원합니다.

Service Fabric은 필요에 따라 작은 규모로 시작하여 수백 또는 수천 대의 컴퓨터가 있는 대규모 환경으로 확장할 수 있는 클라우드 네이티브 서비스를 만들 수 있도록 조정되어 있습니다.

오늘날 인터넷 범위의 서비스는 마이크로 서비스를 토대로 빌드됩니다. 마이크로 서비스의 예로는 프로토콜 게이트웨이, 사용자 프로필, 쇼핑 카트, 인벤토리 처리, 큐, 캐시 등을 들 수 있습니다. 서비스 패브릭은 모든 마이크로 서비스에 상태 비저장 또는 상태 저장 중 하나일 수 있는 고유한 이름을 제공하는 마이크로 서비스 플랫폼입니다.

Service Fabric은 이러한 마이크로 서비스로 구성된 응용 프로그램에 포괄적인 런타임 및 수명 주기 관리 기능을 제공합니다. 서비스 패브릭 클러스터에 걸쳐 배포 및 활성화된 컨테이너 내에서 마이크로 서비스를 호스트합니다. 가상 컴퓨터에서 컨테이너로 이동하면 밀도가 큰 순서대로 정렬됩니다. 마찬가지로, 컨테이너에서 마이크로 서비스로 이동하면 또 다른 밀도 순서대로 정렬됩니다. 예를 들어, 단일 Azure SQL Database 클러스터는 수십만 개의 데이터베이스를 호스트하는 수만 개의 컨테이너를 실행하는 수백 대의 컴퓨터로 구성됩니다. 각 데이터베이스는 서비스 패브릭 상태 저장 마이크로 서비스입니다. 이는 앞서 언급한 다른 서비스에도 적용되며 Service Fabric 기능을 설명하는 데 *하이퍼스케일(hyperscale)*이라는 용어가 사용되는 이유입니다. 컨테이너에서 높은 밀도를 제공한다면 마이크로 서비스에서는 하이퍼스케일을 제공합니다.

마이크로 서비스 접근 방식에 대한 자세한 내용은 [응용 프로그램 구축에 마이크로 서비스 접근 방식이 필요한 이유](service-fabric-overview-microservices.md)를 참조하세요.

## <a name="container-deployment-and-orchestration"></a>컨테이너 배포 및 오케스트레이션
Service Fabric은 컴퓨터 클러스터에 걸쳐있는 마이크로 서비스 [오케스트레이터](service-fabric-cluster-resource-manager-introduction.md) 입니다. 마이크로 서비스는 [Service Fabric 프로그래밍 모델](service-fabric-choose-framework.md) 사용부터 [게스트 실행 파일](service-fabric-deploy-existing-app.md) 배포에 이르는 다양한 방식으로 개발될 수 있습니다. Service Fabric은 컨테이너 이미지에 서비스를 배포할 수 있습니다. 중요한 점은 프로세스의 서비스와 동일한 응용 프로그램의 컨테이너의 서비스를 혼합할 수 있습니다. 컴퓨터 클러스터 전반에 걸쳐 [컨테이너 이미지를 배포 및 관리](service-fabric-containers-overview.md)하려는 경우 Service Fabric이 가장 적합합니다.

## <a name="create-clusters-for-service-fabric-anywhere"></a>어디에서나 Service Fabric용 클러스터 만들기
Azure 또는 온-프레미스, Windows Server 또는 Linux 등 수많은 환경에서 Service Fabric용 클러스터를 만들 수 있습니다. 또한 SDK의 개발 환경은 프로덕션 환경과 동일하며, 관련된 에뮬레이터가 없습니다. 즉, 로컬 개발 클러스터에서 실행되는 것은 다른 환경의 동일한 클러스터에 배포됩니다.

온-프레미스에서 클러스터를 만들기에 대한 자세한 내용은 [Windows Server 또는 Linux에서 클러스터 만들기](service-fabric-deploy-anywhere.md) 또는 Azure의 경우 [Azure Portal을 통해](service-fabric-cluster-creation-via-portal.md) 클러스터 만들기를 참조하세요.

![서비스 패브릭 플랫폼][Image1]

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric용 상태 비저장 및 상태 저장 마이크로 서비스
Service Fabric을 사용하면 마이크로 서비스로 구성된 응용 프로그램을 빌드할 수 있습니다. 상태 비저장 마이크로 서비스(프로토콜 게이트웨이, 웹 프록시 등)는 서비스의 요청 및 응답 이외에 변경 가능한 상태를 관리하지 않습니다. Azure 클라우드 서비스 작업자 역할이 상태 비저장 서비스의 예입니다. 상태 저장 마이크로 서비스(사용자 계정, 데이터베이스, 장치, 쇼핑 카트, 큐 등)는 요청 및 응답 이외에 변경 가능하고 신뢰할 수 있는 상태를 관리합니다. 오늘날 인터넷 범위의 서비스는 상태 비저장 및 상태 저장 마이크로 서비스의 조합으로 구성됩니다.

상태 저장 마이크로 서비스가 상태 비저장 마이크로 서비스와 함께 있는 이유는 무엇인가요? 두 가지 주요 이유가 있습니다.

* 동일한 컴퓨터에 코드와 데이터를 거의 유지하여 높은 처리량, 낮은 대기 시간, 내결함성 온라인 트랜잭션 처리(OLTP) 서비스를 빌드할 수 있습니다. 대화형 인터넷 상점, 검색, IoT(사물 인터넷) 시스템, 거래 시스템, 신용 카드 처리 및 사기 감지 시스템,개인 레코드 관리를 몇 가지 예로 들 수 있습니다.
* 응용 프로그램 디자인을 간소화할 수 있습니다. 상태 저장 마이크로 서비스를 사용하면 순수한 상태 비저장 응용 프로그램의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요로 했던 추가 큐 및 캐시가 필요하지 않습니다. 상태 저장 서비스는 기본적으로 가용성이 높고 대기 시간이 낮아 전체적으로 응용 프로그램에서 관리하는 이동 부분 수가 줄어듭니다.

Service Fabric을 사용하는 응용 프로그램 패턴에 대한 자세한 내용은 서비스에 대한 [응용 프로그램 시나리오](service-fabric-application-scenarios.md) 및 [프로그래밍 모델 프레임워크 선택](service-fabric-choose-framework.md)을 참조하세요.

상태 비저장 및 상태 저장 서비스의 개요에 대해서는 Microsoft Virtual Academy 비디오를 시청할 수도 있습니다.<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">  
<img src="./media/service-fabric-overview/ReliableServicesVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="application-lifecycle-management"></a>응용 프로그램 수명 주기 관리
Service Fabric은 클라우드 응용 프로그램의 전체 응용 프로그램 수명 주기 관리를 지원합니다. 이 수명 주기는 개발부터 배포, 일상적인 관리, 유지 관리, 최종 서비스 해제까지 포함합니다.

Service Fabric 응용 프로그램 수명 주기 관리 기능을 사용하면 응용 프로그램 관리자와 IT 운영자가 간단하고 개입할 필요성이 적은 워크플로를 사용하여 응용 프로그램을 프로비전, 배포, 패치 및 모니터링할 수 있습니다. 이러한 기본 제공 워크플로는 응용 프로그램을 지속적으로 사용 가능하게 유지해야 하는 IT 운영자의 부담을 크게 줄여줍니다.

대부분의 응용 프로그램은 상태 비저장 및 상태 저장 마이크로 서비스, 그리고 함께 배포되는 다른 실행 파일/런타임의 조합으로 구성됩니다. 응용 프로그램 및 패키지된 마이크로 서비스에 대한 강력한 형식을 가지고 있으므로, 서비스 패브릭을 사용하면 여러 응용 프로그램 인스턴스를 배포할 수 있습니다. 각 인스턴스는 독립적으로 관리 및 업그레이드됩니다. 중요한 점은 Service Fabric을 사용하면 *모든* 실행 파일 또는 런타임을 배포하고 안정적으로 만들 수 있다는 것입니다. 예를 들어, Service Fabric은 .NET, ASP.NET Core, Node.js, Java 가상 컴퓨터, 스크립트 또는 응용 프로그램을 구성하는 모든 다른 항목을 배포합니다.

응용 프로그램 수명 주기 관리에 대한 자세한 내용은 [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)를 참조하세요. 모든 코드를 배포하는 방법에 대한 자세한 내용은 [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)를 참조하세요.

앱 수명 주기 관리의 개요에 대해서는 Microsoft Virtual Academy 비디오를 시청할 수도 있습니다.<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">  
<img src="./media/service-fabric-overview/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="key-capabilities"></a>주요 기능
서비스 패브릭을 사용하면 다음을 수행할 수 있습니다.

* 자동 복구되며 대규모 확장 가능한 응용 프로그램 개발.
* Service Fabric 프로그래밍 모델을 사용하여 마이크로 서비스로 구성된 응용 프로그램을 개발합니다. 또는 ASP.NET Core, Node.js 등과 같이 사용자가 선택한 기타 응용 프로그램 프레임워크 및 게스트 실행 파일을 간단히 호스트할 수 있습니다.
* 매우 안정적인 상태 비저장 및 상태 저장 마이크로 서비스를 개발합니다.
* 배포 및 오케스트레이션 컨테이너에는 클러스터 전반의 Windows 컨테이너 및 Docker 컨테이너가 포함됩니다. 이러한 컨테이너는 게스트 실행 파일 또는 신뢰할 수 있는 상태 비저장 및 상태 저장 마이크로 서비스를 포함할 수 있습니다. 두 경우 모두 컨테이너 포트를 호스트 포트에 매핑, 컨테이너 검색 기능 및 자동화된 장애 조치를 가져옵니다.
* 캐시와 큐 대신 상태 저장(마이크로) 서비스를 사용하여 응용 프로그램 디자인 간소화.
* Azure에 또는 코드 변경 없이 Windows Server 또는 Linux를 실행하는 온-프레미스 데이터 센터에 배포. 한 번 작성한 후 모든 Service Fabric 클러스터에 배포.
* "컴퓨터의 데이터 센터" 방식을 사용한 개발. 로컬 개발 환경은 Azure 데이터 센터에서 실행되는 것과 동일한 코드입니다.
* 몇 초 내에 응용 프로그램 배포.
* 컴퓨터당 수백 또는 수천 개의 응용 프로그램을 배포하는 가상 컴퓨터 보다 높은 밀도로 응용 프로그램 배포.
* 동일한 응용 프로그램의 서로 다른 버전을 병렬로 배포하고 각 응용 프로그램을 독립적으로 업그레이드.
* 중단 및 무중단 업그레이드를 포함하여, 중단 시간 없이 상태 저장 응용 프로그램의 수명 주기 관리.
* .NET API, Java(Linux), PowerShell, Azure 명령줄 인터페이스(Linux) 또는 REST 인터페이스를 사용하여 응용 프로그램 관리.
* 응용 프로그램 내의 마이크로 서비스를 독립적으로 업그레이드 및 패치.
* 응용 프로그램의 상태를 모니터링 및 진단하고 자동 복구를 수행하는 정책 설정.
* 클러스터의 노드 수를 증가시키거나 감소시키고 각 노드의 크기를 늘리거나 줄임. 노드 크기를 조정하면 사용 가능한 리소스에 따라 응용 프로그램이 자동으로 크기를 조정하고 배포됩니다.
* 자동 복구되는 리소스 분산 장치를 감시하여 클러스터 간 응용 프로그램의 재배포를 조율합니다. 서비스 패브릭은 오류에서 복구하고 사용 가능한 리소스에 따라 부하의 분산을 최적화합니다.
* 프로덕션 환경에서 실행하기 전에 오류 분석 서비스를 사용하여 서비스에서 카오스 테스트를 수행하여 문제와 오류를 확인합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* 자세한 내용은 다음을 참조하세요.
  * [응용 프로그램 구축에 마이크로 서비스 접근 방식이 필요한 이유](service-fabric-overview-microservices.md)
  * [용어 개요](service-fabric-technical-overview.md)
* 서비스 패브릭 [개발 환경](service-fabric-get-started.md)  
* [프로그래밍 모델 프레임워크 선택](service-fabric-choose-framework.md) 을 읽어보세요.
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

