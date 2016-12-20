---
title: "Service Fabric Explorer를 사용하여 클러스터 시각화 | Microsoft Docs"
description: "서비스 패브릭 탐색기는 Microsoft Azure 서비스 패브릭 클러스터에서 클라우드 응용 프로그램 및 노드를 검사 및 관리하기 위한 웹 기반 도구입니다."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b97cd5d2f9a29d3fa8f13363b937ace276e556ff


---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>서비스 패브릭 탐색기로 클러스터 시각화
서비스 패브릭 탐색기는 Azure 서비스 패브릭 클러스터에서 응용 프로그램 및 노드를 검사 및 관리하기 위한 웹 기반 도구입니다. 클러스터의 실행 여부와 관계없이 항상 사용할 수 있도록 서비스 패브릭 탐색기가 클러스터 내에서 직접 호스트됩니다.

## <a name="connect-to-service-fabric-explorer"></a>서비스 패브릭 탐색기에 연결
[개발 환경 준비](service-fabric-get-started.md)에 대한 지침을 따른 경우 http://localhost:19080/Explorer로 이동하여 로컬 클러스터에서 Service Fabric Explorer를 시작할 수 있습니다.

> [!NOTE]
> 서비스 패브릭 탐색기에서 Internet Explorer를 사용하여 원격 클러스터를 관리하는 경우 일부 Internet Explorer 설정을 구성해야 합니다. 모든 정보가 올바르게 로드되도록 하려면 **도구** > **호환성 보기 설정**으로 이동하고 **호환성 보기에서 인트라넷 사이트 표시**를 선택 취소합니다.
> 
> 

## <a name="understand-the-service-fabric-explorer-layout"></a>서비스 패브릭 탐색기 레이아웃 이해
왼쪽의 트리를 사용하여 서비스 패브릭 탐색기를 탐색할 수 있습니다. 트리의 루트에서 클러스터 대시보드는 응용 프로그램 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다.

![서비스 패브릭 탐색기 클러스터 대시보드][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>클러스터의 레이아웃 보기
서비스 패브릭 클러스터의 노드는 장애 도메인 및 업그레이드 도메인의 2차원 그리드에 배치됩니다. 이렇게 배치하면 하드웨어 오류와 응용 프로그램 업그레이드가 있는 상태에서 응용 프로그램을 계속 사용할 수 있습니다. 클러스터 맵을 사용하여 현재 클러스터의 레이아웃 방식을 볼 수 있습니다.

![서비스 패브릭 탐색기 클러스터 맵][sfx-cluster-map]

### <a name="view-applications-and-services"></a>응용 프로그램 및 서비스 보기
클러스터에는 두 개의 하위 트리가 포함되어 있습니다. 하나는 응용 프로그램용이고 다른 하나는 노드용입니다.

응용 프로그램 보기를 사용하여 서비스 패브릭의 논리 계층 구조인 응용 프로그램, 서비스, 파티션 및 복제를 탐색할 수 있습니다.

아래 예제에서 응용 프로그램 **MyApp**은 두 개의 서비스, **MyStatefulService**와 **WebService**로 구성되어 있습니다. **MyStatefulService** 는 상태 저장이므로 한 개의 주 복제본과 두 개의 보조 복제본이 있는 파티션을 포함합니다. 이와 반대로 WebSvcService는 상태 비저장이며 단일 인스턴스가 들어 있습니다.

![서비스 패브릭 탐색기 응용 프로그램 보기][sfx-application-tree]

트리의 각 수준에서 주 창은 항목에 대한 관련 정보를 표시합니다. 예를 들어 특정 서비스에 대한 상태 및 버전을 볼 수 있습니다.

![서비스 패브릭 탐색기 기능 창][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>클러스터의 노드 보기
노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 응용 프로그램을 검사할 수 있습니다. 특히 현재 실행되고 있는 복제본을 확인할 수 있습니다.

## <a name="actions"></a>작업
서비스 패브릭 탐색기는 클러스터 내에서 노드, 응용 프로그램 및 서비스에 대한 작업을 호출하는 빠른 방법을 제공합니다.

예를 들어 응용 프로그램 인스턴스를 삭제하려면 왼쪽 트리에서 응용 프로그램을 선택한 다음 **작업** > **응용 프로그램 삭제**로 이동하여 로컬 클러스터에서 서비스 패브릭 탐색기를 시작할 수 있습니다.

![서비스 패브릭 탐색기에서 응용 프로그램 삭제][sfx-delete-application]

> [!TIP]
> 각 요소 옆의 줄임표를 클릭하여 동일한 작업을 수행할 수 있습니다.
> 
> 

다음 테이블에서는 각 엔터티에 사용할 수 있는 작업을 나열합니다.

| **엔터티** | **작업** | **설명** |
| --- | --- | --- |
| 응용 프로그램 형식 |프로 비전 해제 형식 |클러스터의 이미지 저장소에서 응용 프로그램 패키지 제거 해당 형식의 모든 응용 프로그램을 먼저 제거해야 합니다. |
| 응용 프로그램 |응용 프로그램 삭제 |모든 서비스와 해당 상태를 포함하여(있는 경우) 응용 프로그램을 삭제합니다. |
| 부여 |서비스 삭제 |서비스 및 해당 상태(있는 경우)를 삭제합니다. |
| 노드 |활성화 |노드 활성화 |
| 비활성화(일시 중지) |현재 상태에서 노드를 일시 중지합니다. 서비스는 계속 실행하지만 가동 중단 또는 데이터 불일치를 방지하는 데 필요한 경우가 아니면 서비스 패브릭은 아무것도 사전에 이동하지 않습니다. 이 작업은 검사하는 동안 이동하지 않도록 특정 노드에 디버깅 서비스를 사용하도록 설정하는 데 일반적으로 사용됩니다. | |
| 비활성화(다시 시작) |안전하게 노드에서 모든 메모리 내 서비스를 이동하고 영구 서비스를 닫습니다. 호스트 프로세스 또는 컴퓨터를 다시 시작해야 할 때 일반적으로 사용됩니다. | |
| 비활성화(데이터 제거) |안전하게 충분한 예비 복제본을 작성한 후에 노드에 실행하는 모든 서비스를 닫습니다. 노드(또는 최소한 저장소)가 위원회에서 영구적으로 제거될 때 일반적으로 사용됩니다. | |
| 노드 상태 제거 |클러스터에서 노드의 복제본에 대한 정보를 제거합니다. 이미 실패한 노드를 복구할 수 없다고 간주하는 경우 일반적으로 사용됩니다. | |

많은 작업이 안전하지 않으므로 작업이 완료되기 전에 실행할 것인지 묻는 메시지가 나타납니다.

> [!TIP]
> 서비스 패브릭 탐색기를 통해 수행할 수 있는 모든 작업은 PowerShell 또는 REST API를 통해 수행할 수 있으므로 자동화를 사용하도록 설정할 수 있습니다.
> 
> 

또한 Service Fabric Explorer를 사용하여 지정된 응용 프로그램 형식 및 버전에 대한 새 응용 프로그램 인스턴스를 만들 수도 있습니다. 트리 보기에서 응용 프로그램 형식을 선택하고 오른쪽 창에서 원하는 버전 옆의 **앱 인스턴스 만들기** 링크를 클릭합니다.

![Service Fabric Explorer에서 응용 프로그램 인스턴스 만들기][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer를 통해 만든 응용 프로그램 인스턴스는 현재 매개 변수화될 수 없습니다. 이러한 프로그램은 기본 매개 변수 값을 사용하여 만들어집니다.
> 
> 

## <a name="connect-to-a-remote-service-fabric-cluster"></a>원격 서비스 패브릭 클러스터에 연결
서비스 패브릭 탐색기는 웹 기반이고 클러스터 내에서 실행되므로 클러스터의 끝점을 알고 있고 여기에 액세스할 수 있는 충분한 권한이 있는 한 모든 브라우저에서 액세스할 수 있습니다.

### <a name="discover-the-service-fabric-explorer-endpoint-for-a-remote-cluster"></a>서비스 패브릭 탐색기에서 원격 클러스터의 끝점 검색
지정된 클러스터를 위한 서비스 패브릭 탐색기에 도달하려면 브라우저를 다음으로 연결하기만 하면 됩니다.

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

전체 URL은 Azure 포털의 클러스터 필수 창에서도 사용 가능합니다.

### <a name="connect-to-a-secure-cluster"></a>보안 클러스터에 연결
인증서 또는 AAD(Azure Active Directory)를 사용하여 서비스 패브릭 클라이언트에 대한 클라이언트 액세스를 제어할 수 있습니다.

보안 클러스터에 Service Fabric Explorer를 연결하려고 하는 경우 클러스터의 구성에 따라 클라이언트 인증서를 제시하거나 AAD를 사용하여 로그인해야 합니다.

## <a name="next-steps"></a>다음 단계
* [테스트 용이성 개요](service-fabric-testability-overview.md)
* [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell을 사용하여 서비스 패브릭 응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png



<!--HONumber=Nov16_HO3-->


