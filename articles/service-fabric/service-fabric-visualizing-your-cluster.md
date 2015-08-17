<properties
   pageTitle="서비스 패브릭 탐색기를 사용하여 클러스터 시각화"
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
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# 서비스 패브릭 탐색기를 사용하여 클러스터 시각화

서비스 패브릭 탐색기는 Microsoft Azure 서비스 패브릭 클러스터에서 클라우드 응용 프로그램 및 노드를 검사 및 관리하기 위한 시각적 도구입니다. 서비스 패브릭 탐색기는 로컬 개발 클러스터와 Azure 클러스터 모두에 연결할 수 있습니다. 서비스 패브릭 PowerShell cmdlet에 대한 정보는 **다음 단계**에서 참조하십시오.

> [AZURE.NOTE]Azure에서 서비스 패브릭 클러스터 만들기는 아직 제공되지 않습니다.

## 서비스 패브릭 탐색기 소개

[서비스 패브릭 개발 환경 설정](service-fabric-get-started.md)에 있는 지침을 따라 로컬 개발 환경이 설정되었는지 확인합니다.

로컬 설치 경로(%Program Files%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe)에서 서비스 패브릭 탐색기를 실행합니다. 도구가 있는 경우에는 자동으로 로컬 개발 클러스터에 연결합니다. 클러스터에 정보를 다음과 같이 표시합니다.

- 클러스터에서 실행 중인 응용 프로그램
- 클러스터의 노드에 대한 정보
- 응용 프로그램 및 노드로부터 상태 이벤트
- 클러스터 내 응용 프로그램의 로드
- 응용 프로그램 업그레이드 상태에 대한 모니터링

![서비스 패브릭 클러스터 및 배포된 응용 프로그램의 시각적 표현][servicefabricexplorer]

중요한 시각화 중 하나는 클러스터용 대시보드에 표시되는 클러스터 맵입니다(예: **Onebox/Local cluster** 클릭). 클러스터 맵은 업그레이드 도메인 및 장애 도메인 집합과 어떤 노드가 어떤 도메인에 매핑되는지를 보여줍니다. [서비스 패브릭의 기술 개요](service-fabric-technical-overview.md)를 참조하여 주요 서비스 패브릭 개념을 숙지하십시오.

![클러스터 맵은 각 노드가 속한 업그레이드 도메인과 오류 도메인을 표시합니다.][clustermap]


## 응용 프로그램 및 서비스 보기

서비스 패브릭 탐색기를 사용하여 클러스터에서 실행 중인 응용 프로그램을 탐색할 수 있습니다. **응용 프로그램 보기**를 확장하여 응용 프로그램, 서비스, 파티션 및 복제본에 대한 자세한 정보를 확인합니다.

아래 다이어그램은 **"fabric:/Stateful1Application"**이라는 이름의 응용 프로그램에 **"fabric:/Stateful1Application/MyFrontEnd"**라는 이름의 상태 비저장 서비스 1개와 **"fabric:/Stateful1Application/Stateful1"**이라는 이름의 상태 저장 서비스가 1개 있음을 보여줍니다. 상태 비저장 서비스에는 **Node.4**에서 실행 중인 1개의 복제본이 있는 파티션이 1개 있습니다. 상태 저장 서비스에는 몇 개의 노드에서 실행 중인 3개의 복제본이 각각 있는 2개의 파티션이 있습니다.

![서비스 패브릭 클러스터에서 실행 중인 응용 프로그램의 보기][applicationview]

응용 프로그램, 서비스, 파티션 또는 복제본을 클릭하면 해당 엔터티에 대한 자세한 정보가 제공됩니다. 아래 다이어그램은 상태 저장 서비스의 주 복제본 하나에 대한 서비스 복제본 상태 대시보드를 표시합니다. 여기에는 해당 역할, 실행 중인 노드, 수신하는 주소, 디스크 상에서 파일의 위치 및 상태 이벤트가 포함됩니다.

![서비스 패브릭 복제본에 대한 자세한 정보][replicadetails]


## 원격 서비스 패브릭 클러스터에 연결

원격 서비스 패브릭 클러스터를 보려면 **연결**을 클릭하여 **서비스 패브릭 클러스터에 연결** 대화 상자를 표시합니다. 클러스터에 **ServiceFabric 끝점**을 입력하고 **연결**을 클릭합니다. 서비스 패브릭 끝점은 일반적으로 포트 19000에서 수신되는 클러스터 서비스의 공개 이름입니다.

![원격 서비스 패브릭 클러스터에 대한 연결 설정][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

- [테스트 용이성 개요](service-fabric-testability-overview.md).
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md).
- [PowerShell을 사용하여 서비스 패브릭 응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png

<!---HONumber=August15_HO6-->