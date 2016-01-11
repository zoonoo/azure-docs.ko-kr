<properties
   pageTitle="Visual Studio에서 응용 프로그램 디버그 | Microsoft Azure"
   description="Visual Studio 및 로컬 개발 클러스터를 사용한 개발 및 디버깅으로 서비스의 안정성과 성능을 향상시킵니다."
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
   ms.date="10/15/2015"
   ms.author="jesseb"/>

# Visual Studio를 사용하여 서비스 패브릭 응용 프로그램 디버그

로컬 컴퓨터 개발 클러스터에서 Azure 서비스 패브릭 응용 프로그램을 배포하고 디버그하여 시간과 비용을 절약할 수 있습니다. Visual Studio는 로컬 클러스터에 응용 프로그램을 배포하고 응용 프로그램의 모든 인스턴스에 디버거를 자동으로 연결할 수 있습니다.

1. [서비스 패브릭 개발 환경 설정](service-fabric-get-started.md)의 단계를 따라 로컬 개발 클러스터를 시작합니다.

2. **F5** 키를 누르거나 **디버그** > **디버깅 시작**을 클릭합니다.

    ![응용 프로그램 디버깅 시작][startdebugging]

3. 코드의 중단점을 설정하고 **디버그** 메뉴의 명령을 클릭하여 응용 프로그램의 단계를 진행합니다.

    > [AZURE.NOTE]Visual Studio는 응용 프로그램의 모든 인스턴스에 연결합니다. 단계별로 코드를 실행하는 동안 중단점은 동시 세션에서 발생하는 여러 프로세스에 히트될 수 있습니다. 스레드 ID의 각 중단점을 조건부로 생성하거나 진단 이벤트를 사용하여 히트된 후에 중단점을 사용하지 않도록 설정합니다.

4. **진단 이벤트** 창이 자동으로 열려서 실시간으로 진단 이벤트를 볼 수 있습니다.

    ![실시간으로 진단 이벤트 보기][diagnosticevents]

5. 서버 탐색기의 **진단 이벤트** 창도 열 수 있습니다. **Azure**에서 **서비스 패브릭 클러스터**를 마우스 오른쪽 단추로 클릭 > **진단 이벤트 보기**를 클릭합니다.

    ![진단 이벤트 창 열기][viewdiagnosticevents]

6. 진단 이벤트는 자동으로 생성되는 **ServiceEventSource.cs** 파일에서 볼 수 있으며 응용 프로그램 코드에서 호출됩니다.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. **진단 이벤트** 창은 필터링, 일시 중지 및 실시간 이벤트 검사를 지원합니다. 필터는 해당 콘텐츠를 포함하는 이벤트 메시지의 단순 문자열 검색입니다.

    ![필터링, 일시 중지, 다시 시작 또는 실시간으로 이벤트 검사][diagnosticeventsactions]

8. 서비스 디버깅은 다른 모든 응용 프로그램의 디버깅과 유사합니다. 중단점은 Visual Studio를 통해 디버깅하기 쉽도록 정상적으로 설정할 수 있습니다. 신뢰할 수 있는 컬렉션은 여러 노드에 걸쳐 복제되어도 여전히 IEnumerable을 구현합니다. 따라서 디버그하는 동안 Visual Studio에서 결과 뷰를 사용하여 내부에 저장한 내용을 볼 수 있습니다. 코드의 아무 곳에나 중단점을 설정하기만 하면 됩니다.

    ![응용 프로그램 디버깅 시작][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

- [서비스 패브릭 서비스 테스트](service-fabric-testability-overview.md)
- [Visual Studio에서 서비스 패브릭 응용 프로그램 관리](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png

<!---HONumber=AcomDC_1223_2015-->