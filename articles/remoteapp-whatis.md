<properties title="What is RemoteApp?" pageTitle="What is RemoteApp?" description="Learn about RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="elizapo" />

# Azure RemoteApp이란?

RemoteApp을 사용하면 Azure를 통해 원격으로 액세스하는 프로그램을 최종 사용자의 로컬 컴퓨터에서 실행하는 것처럼 표시할 수 있습니다. 이러한 프로그램을 RemoteApp 프로그램이라고 합니다. 원격 데스크톱 세션 호스트(RD 세션 호스트) 서버의 데스크톱에서 사용자에게 표시하는 대신 RemoteApp 프로그램은 클라이언트의 데스크톱과 통합됩니다. RemoteApp 프로그램은 크기 조정 가능한 자체 창에서 실행되며, 여러 모니터 간에 드래그할 수 있으며, 작업 표시줄에 자체 항목을 포함합니다. 사용자가 동일한 RD 세션 호스트 서버에서 RemoteApp 프로그램을 두 개 이상 실행하는 경우 RemoteApp 프로그램은 동일한 원격 데스크톱 서비스 세션을 공유합니다.

RemoteApp은 다음을 비롯하여 다양한 상황에서 복잡도와 관리 오버헤드를 줄일 수 있습니다.

-   로컬 IT 지원과 네트워크 대역폭이 제한된 지사
-   사용자가 원격으로 프로그램에 액세스해야 하는 상황
-   LOB(기간 업무) 응용 프로그램, 특히 사용자 지정 LOB 프로그램의 배포
-   사용자를 컴퓨터에 할당할 수 없는 환경(예: "핫 데스크" 또는 "근무 좌석 공동 이용" 작업 영역)
-   다양한 버전의 프로그램 배포, 특히 여러 버전을 로컬로 설치하면 충돌이 발생합니다.

기존의 원격 데스크톱 서비스와 달리 Azure RemoteApp은 Azure 관리 포털에서 실행합니다. 사용자는 포털을 통해 프로그램에 액세스하며, 관리자는 관리 포털을 통해 프로그램 및 사용자에 대한 모는 관리를 수행합니다.

다음과 같은 두 가지 종류의 RemoteApp 배포가 있습니다.

-   클라우드 배포가 호스트되며 Azure 클라우드의 프로그램에 대한 모든 데이터를 저장합니다.
-   하이브리드 배포는 Azure 클라우드에 호스트되지만 이를 통해 사용자는 로컬 네트워크에 저장된 데이터에 액세스할 수 있습니다.

선택하는 배포 유형과 상관없이 서비스를 만든 후 사용자와 공유하는 프로그램을 최종 사용자 피드에 게시합니다. 피드는 사용자가 Azure 포털을 통해 액세스하는 사용 가능한 프로그램의 목록으로, 구독과 연결된 모든 서비스의 모든 프로그램을 보여 줍니다.

