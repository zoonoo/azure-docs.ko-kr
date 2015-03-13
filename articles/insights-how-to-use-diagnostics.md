<properties 
	pageTitle="진단을 사용하는 방법" 
	description="Azure에서 리소스에 대한 진단을 설정하는 방법에 대해 알아봅니다." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>
# 진단 설정

Azure 포털 미리 보기에서는 이제 Windows 가상 컴퓨터와 저장소 계정과 관련하여 빈번하게 사용되는 다양한 모니터링 및 진단 데이터를 구성할 수 있습니다.

## 가상 컴퓨터에서 다양한 데이터 수집
1. [Azure 포털 미리 보기](https://portal.azure.com/)에서 **찾아보기**를 클릭하고 **가상 컴퓨터**를 클릭한 후 모니터링할 가상 컴퓨터를 선택합니다.
2. **모니터링** 렌즈에는 **CPU 비율**, **디스크 읽기 및 쓰기**, **네트워크 수신 및 송신** 등의 일부 기본 메트릭이 포함되어 있습니다. 이러한 항목을 클릭하면 **메트릭** 블레이드가 표시됩니다.  
    ![Monitoring lens](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3. **메트릭** 블레이드에는 선택한 메트릭에 대한 자세한 정보가 표시됩니다. 블레이드 맨 위에는 그래프가 있고, 그 아래에 평균, 최소값, 최대값 등 이 메트릭의 집계를 보여 주는 테이블이 있습니다. 그 아래에는 정의한 경고의 목록이 블레이드에 표시되는 메트릭으로 필터링되어 나옵니다.  
    ![Metric blade](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4. 다양한 진단을 사용하도록 설정하려면 **설정** 단추를 클릭합니다. 그러면 **진단** 블레이드가 표시됩니다. **ON**을 선택합니다.  
    ![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)
    - **기본 메트릭**: 프로세서, 메모리 등 가상 컴퓨터에 대한 상태 메트릭입니다. 
    - **디스크별 메트릭**: 가상 컴퓨터에 연결된 모든 디스크에 대한 메트릭입니다.
    - **.NET 메트릭**: 가상 컴퓨터에서 실행되는 .NET 및 ASP.NET 응용 프로그램에 대한 메트릭입니다.
    - **네트워크 메트릭**: 네트워크 연결 및 웹 서비스에 대한 메트릭입니다.
    - **Windows 이벤트 응용 프로그램 로그**: 응용 프로그램 채널로 전송되는 Windows 이벤트입니다.
    - **Windows 이벤트 시스템 로그**: 시스템 채널로 전송되는 Windows 이벤트입니다. [Microsoft 맬웨어 방지 프로그램](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)의 모든 이벤트도 포함됩니다. 
    - **Windows 이벤트 보안 로그**: 보안 채널로 전송되는 Windows 이벤트입니다.
    - **진단 인프라 로그**: 진단 수집 인프라에 대한 로깅입니다.
    - **IIS 로그**: IIS 서버에 대한 로그입니다.
    모든 메트릭과 로그는 1분 간격으로 기록되므로 컴퓨터에 대한 최신 정보를 항상 확인할 수 있습니다.

저장소 계정에 대해 진단 정보를 사용하도록 설정하면 해당 계정에 일반 저장소, 트랜잭션 및 송신 비용이 청구됩니다. 그러나 이러한 기능은 많은 데이터를 생성하지 않습니다(IIS 로그의 경우는 예외일 수 있음). 송신 비용을 최소화하려면 같은 지역의 저장소 계정을 가상 컴퓨터로 선택해야 합니다.

**확인**을 클릭하면 몇 분 이내에 데이터가 저장소 계정에 표시되기 시작합니다. Linux를 실행하는 가상 컴퓨터에 대해서는 진단을 사용하도록 설정할 수 없으며 진단을 사용하려면 게스트 에이전트를 설치해야 합니다.

## 저장소 계정에서 다양한 데이터 수집

이전에도 저장소 계정에서 일부 데이터를 수집할 수는 있었지만 Azure 포털 미리 보기에서는 이제 1분 단위로 데이터를 수집할 수 있으므로 저장소 계정 내에서 수행되는 작업을 완벽하게 파악할 수 있습니다. 1분 메트릭을 사용하도록 설정하는 단계는 가상 컴퓨터에서의 단계와 비슷합니다.

1. **저장소 계정** 블레이드에서 아무 차트나 클릭하여 **메트릭** 블레이드로 이동합니다.
2. 명령 모음에서 **진단** 단추를 클릭합니다.
3. 저장소 계정에서 수집할 데이터를 선택합니다.  
    ![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. **확인**을 클릭합니다. 데이터가 처음으로 표시될 때까지는 몇 분 정도 걸립니다.

## 진단 데이터 시각화 

진단을 사용하도록 설정한 후 차트를 마우스 오른쪽 단추로 클릭하고 **쿼리 편집**으로 이동하면 사용 가능한 전체 메트릭 목록을 확인할 수 있습니다.

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

이러한 메트릭을 플로팅할 수 있고, **지난 시간**으로 확대하거나 **지난 주**로 축소할 수도 있으며, **사용자 지정** 시간 범위를 선택할 수도 있습니다.
 
![Custom timerange](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

이러한 메트릭은 이전에 제공되었던 데이터에 비해 훨씬 세밀하며 지연 시간은 최소한으로 유지됩니다.

현재는 프로세스별 메트릭, 디스크별 메트릭 등 인스턴스가 여러 개인 메트릭을 그림으로 나타낼 수 없습니다. 모니터링 차트를 사용자 지정하는 방법에 대한 자세한 내용은 [모니터링을 사용자 지정하는 방법](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409)을 참조하세요.

## 진단 데이터 관련 경고

포털 미리 보기에서는 메트릭을 시각화할 수 있을 뿐 아니라 이러한 메트릭에 대해 경고를 표시할 수도 있습니다. 이렇게 하려면 먼저 가상 컴퓨터 또는 저장소 블레이드에서 아래쪽의 **경고 규칙** 파트로 스크롤한 다음 **경고 추가**를 클릭합니다.

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

그런 다음 진단을 사용하도록 설정한 메트릭 중에서 원하는 항목을 선택할 수 있습니다.

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

그래프에는 전날의 메트릭과 비교한 경고 임계값 미리 보기가 표시됩니다. **저장**을 클릭하면 몇 분 이내에 선택한 메트릭이 임계값을 초과할 때마다 알림이 표시됩니다. 

미리 보기 포털에서만 표시되는 메트릭에 대해서는 전체 포털에서 경고를 받을 수 없습니다. 따라서 미리 보기 포털의 특정 경고 규칙은 전체 포털에 표시되지 않습니다.

<!--HONumber=46--> 
