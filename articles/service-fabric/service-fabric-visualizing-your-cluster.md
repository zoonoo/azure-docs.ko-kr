<properties
   pageTitle="서비스 패브릭 탐색기를 사용하여 클러스터 시각화 | Microsoft Azure"
   description="서비스 패브릭 탐색기는 Microsoft Azure 서비스 패브릭 클러스터에서 클라우드 응용 프로그램 및 노드를 검사 및 관리하기 위한 유용한 GUI 도구입니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# 서비스 패브릭 탐색기를 사용하여 클러스터 시각화

서비스 패브릭 탐색기는 Azure 서비스 패브릭 클러스터에서 응용 프로그램 및 노드를 검사 및 관리하기 위한 웹 기반 도구입니다. 클러스터의 실행 여부와 관계없이 항상 사용할 수 있도록 서비스 패브릭 탐색기가 클러스터 내에서 직접 호스트됩니다.

## 서비스 패브릭 탐색기에 연결

[개발 환경 준비](service-fabric-get-started.md)에 대한 지침을 따른 경우 http://localhost:19080/Explorer로 이동하여 로컬 클러스터에서 서비스 패브릭 탐색기를 시작할 수 있습니다.

>[AZURE.NOTE]서비스 패브릭 탐색기에서 Internet Explorer를 사용하여 원격 클러스터를 관리하는 경우 일부 Internet Explorer 설정을 구성해야 합니다. **도구** -> **호환성 보기 설정**으로 이동하고 **호환성 보기에서 인트라넷 사이트 표시**를 선택 취소하여 모든 정보가 올바르게 로드되도록 합니다.

## 서비스 패브릭 탐색기 레이아웃 이해

왼쪽의 트리를 사용하여 서비스 패브릭 탐색기를 탐색할 수 있습니다. 트리의 루트에서 클러스터 대시보드는 응용 프로그램 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다.

![서비스 패브릭 탐색기 클러스터 대시보드][sfx-cluster-dashboard]

### 클러스터의 레이아웃 보기

서비스 패브릭 클러스터의 노드는 장애 도메인 및 업그레이드 도메인의 2차원 그리드에 배치됩니다. 이렇게 배치하면 하드웨어 오류와 응용 프로그램 업그레이드가 있는 상태에서 응용 프로그램을 계속 사용할 수 있습니다. 클러스터 맵을 사용하여 현재 클러스터의 레이아웃 방식을 볼 수 있습니다.

![서비스 패브릭 탐색기 클러스터 맵][sfx-cluster-map]

### 응용 프로그램 및 서비스 보기

클러스터에는 두 개의 하위 트리가 포함되어 있습니다. 하나는 응용 프로그램용이고 다른 하나는 노드용입니다.

응용 프로그램 보기를 사용하여 서비스 패브릭의 논리 계층 구조인 응용 프로그램, 서비스, 파티션 및 복제를 탐색할 수 있습니다.

아래 예제에서 응용 프로그램 **MyApp**은 두 개의 서비스, **MyStatefulService**와 **WebService**로 구성되어 있습니다. **MyStatefulService**는 상태 저장이므로 한 개의 주 복제본과 두 개의 보조 복제본이 있는 파티션을 포함합니다. 이와 반대로 WebSvcService는 상태 비저장이며 단일 인스턴스가 들어 있습니다.

![서비스 패브릭 탐색기 응용 프로그램 보기][sfx-application-tree]

트리의 각 수준에서 주 창은 항목에 대한 관련 정보를 표시합니다. 예를 들어 특정 서비스에 대한 상태 및 버전을 볼 수 있습니다.

![서비스 패브릭 탐색기 기능 창][sfx-service-essentials]

### 클러스터의 노드 보기

노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 응용 프로그램을 검사할 수 있습니다. 특히 현재 실행되고 있는 복제본을 확인할 수 있습니다.

## 서비스 패브릭 탐색기를 사용하여 작업 수행

서비스 패브릭 탐색기는 클러스터 내에서 노드, 응용 프로그램 및 서비스에 대한 작업을 호출하는 빠른 방법을 제공합니다.

예를 들어 응용 프로그램 인스턴스를 삭제하려면 왼쪽 트리에서 응용 프로그램을 선택한 다음 **작업** > **응용 프로그램 삭제**를 선택합니다.

![서비스 패브릭 탐색기에서 응용 프로그램 삭제][sfx-delete-application]

많은 작업이 안전하지 않으므로 작업이 완료되기 전에 정말로 삭제할 것인지 묻는 메시지가 나타납니다.

>[AZURE.NOTE]서비스 패브릭 탐색기를 통해 수행할 수 있는 모든 작업은 PowerShell 또는 REST API를 통해 수행할 수 있으므로 자동화를 사용하도록 설정할 수 있습니다.



## 원격 서비스 패브릭 클러스터에 연결

서비스 패브릭 탐색기는 웹 기반이고 클러스터 내에서 실행되므로 클러스터의 끝점을 알고 있고 여기에 액세스할 수 있는 충분한 권한이 있는 한 모든 브라우저에서 액세스할 수 있습니다.

### 서비스 패브릭 탐색기에서 원격 클러스터의 끝점 검색

지정된 클러스터를 위한 서비스 패브릭 탐색기에 도달하려면 브라우저를 다음으로 연결하기만 하면 됩니다.

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

전체 URL은 Azure 포털의 클러스터 필수 창에서도 사용 가능합니다.

### 보안 클러스터에 연결

연결하려면 인증서를 제공하도록 클라이언트에 요청하여 서비스 패브릭 클러스터에 대한 액세스를 제어할 수 있습니다.

보안 클러스터에서 서비스 패브릭 탐색기에 연결하려는 경우 액세스 권한을 얻으려면 인증서를 제공하라는 메시지가 브라우저에 표시됩니다.

## 다음 단계

- [테스트 용이성 개요](service-fabric-testability-overview.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)
- [PowerShell을 사용하여 서비스 패브릭 응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_0114_2016-->