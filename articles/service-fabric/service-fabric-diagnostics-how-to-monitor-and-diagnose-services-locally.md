<properties
   pageTitle="Microsoft Azure 서비스 패브릭 - 로컬에서 서비스를 모니터링 및 진단하는 방법"
   description="이 문서에서는 로컬 개발 컴퓨터에서 Microsoft Azure 서비스 패브릭을 이용하여 쓰인 서비스를 모니터링하고 진단할 수 있는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/04/2015"
   ms.author="kunalds"/>


# 로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스
모니터링, 검색, 진단 및 문제 해결은 사용자 환경에 최소화하면서 서비스를 계속할 수 있게 해 줍니다. 이는 실제 배포된 프로덕션 환경에서 중요하지만, 효율성은 실제 사용 설정으로 이행했을 때 작동을 보장하는 서비스 개발 중의 유사한 모델의 채택에 좌우됩니다. 서비스 패브릭은 서비스 개발자가 단일 컴퓨터 로컬 개발과 실제 사용 프로덕션 클러스터 설정 간에서 매끄럽게 작동할 수 있는 실제 진단을 구현화하기 쉽게 해 줍니다.

## 추적 및 로깅에 ETW를 사용하는 이유
[Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW)은 서비스 패브릭의 추적 메시지용으로 바람직한 기술입니다. 그 이유는 다음과 같습니다.

* ETW는 속도가 빠릅니다. 코드 실행 시간에 미치는 영향이 최소화된 추적 기술로 빌드되었습니다.
* ETW 추적은 로컬 개발 환경 및 실제 사용 클러스터 설정에서도 매끄럽게 작동합니다. 즉 실제 클러스터에 코드를 배포할 준비가 되었을 때 추적 코드를 다시 쓸 필요가 없습니다.
* 서비스 패브릭은 내부 추적에도 ETW를 사용합니다. 따라서 기본 시스템에서 AND 시퀀스 및 응용 프로그램 코드와 이벤트간의 상호 관계를 더 쉽게 이해할 수 있도록 하는 서비스 패브릭 시스템 추적으로 인터리브된 응용 프로그램 추적을 볼 수 있습니다.
* 서비스 패브릭 Visual Studio 도구는 ETW 이벤트 보기를 내부적으로 지원합니다.


## Visual Studio에서 서비스 패브릭 시스템 이벤트 보기

서비스 패브릭은 ETW 이벤트를 내보내서 응용 프로그램 개발자가 플랫폼에서 일어나는 일을 이해할 수 있도록 도와줍니다. 추적 메시지를 표시하는 진단 이벤트 뷰어와 함께 응용 프로그램을 실행하려면 [Visual Studio에서 첫 응용 프로그램 만들기](./service-fabric-create-your-first-application-in-visual-studio.md)의 단계를 따르세요(아직 수행하지 않은 경우).

1. 진단 이벤트 창이 자동으로 표시되지 않으면 Visual Studio의 서버 탐색기 탭으로 가서 서비스 패브릭 클러스터를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 “진단 이벤트 보기”를 선택하십시오.

  ![Visual Studio 진단 이벤트 뷰어 열기](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

2. 각 이벤트는 이벤트가 기인하는 노드, 응용 프로그램 및 서비스를 알려 주는 표준 메타 데이터 정보를 가지고 있습니다. 창 위쪽의 “이벤트 필터링” 상자를 사용해서 이벤트 목록을 필터링할 수도 있습니다. 예를 들어 노드 이름이나 서비스 이름으로 필터링할 수 있습니다. 또한 이벤트 정보를 볼 때 창 위쪽의 단추를 사용하여 일시 중지하고 나중에 이벤트 손실 없이 재개할 수 있습니다.

  ![Visual Studio 진단 이벤트 뷰어](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## 응용 프로그램 코드에 독자적인 사용자 지정 추적 추가
서버 패브릭 Visual Studio 프로젝트 템플릿에는 샘플 코드가 포함됩니다. 이 코드는 서비스 패브릭에서 시스템 추적과 함께 Visual Studio ETW 뷰어에 표시되는 사용자 지정 응용 프로그램 코드 ETW 추적을 추가하는 방법을 보여 줍니다. 이 방법의 장점은 메타 데이터가 추적에 자동으로 추가되고 Visual Studio 진단 뷰어가 이를 표시하도록 이미 구성되어 있다는 점입니다.

**서비스 템플릿**(상태 비저장 또는 상태 저장)에서 만들어진 프로젝트의 경우 `RunAsync` 구현을 검색하기만 하면 됩니다.

1. `RunAsync` 메서드의 `ServiceEventSource.Current.ServiceMessage` 호출은 응용 프로그램 코드에서 사용자 지정 ETW 추적의 예를 보여 줍니다.
2. **ServiceEventSource.cs** 파일에서 성능상의 이유로 빈도가 높은 이벤트에 사용해야 하는 `ServiceEventSource.ServiceMessage` 메서드에 대한 오버로드를 확인합니다.

**행위자 템플릿**(상태 비저장 또는 상태 저장)에서 만들어진 프로젝트의 경우:

1. *ProjectName*이 Visual Studio 프로젝트용으로 선택한 이름인 **“ProjectName”.cs** 파일을 엽니다.  
2. *DoWorkAsync* 메서드에서 코드 `ActorEventSource.Current.ActorMessage(this, "Doing Work");`을(를) 찾습니다. 이는 응용 프로그램 코드에서 작성된 사용자 지정 ETW의 예제입니다.  
3. **ActorEventSource.cs** 파일에서 성능상의 이유로 빈도가 높은 이벤트에 사용해야 하는 `ActorEventSource.ActorMessage` 메서드에 대한 오버로드를 확인합니다.

서비스 코드에 사용자 지정 ETW 추적을 추가한 다음에는 응용 프로그램을 다시 빌드, 배포, 실행하여 진단 뷰어에서 이벤트를 볼 수 있습니다. F5 키를 눌러서 응용 프로그램을 디버깅하면 진단 뷰어가 자동으로 열립니다.

## 다음 단계
로컬 진단을 위해 위에서 응용 프로그램에 추가한 것과 동일한 추적 코드는 Azure 클러스터에서 응용 프로그램을 실행할 때 이러한 이벤트를 보는 데 사용할 수 있는 도구와 함께 작동합니다. 도구에 대한 다양한 옵션 및 이를 설정하는 방법은 다음 문서를 참조하세요. * [MAD(Microsoft Azure 진단) 및 Operational Insights를 사용하여 Azure에서 서비스 패브릭 클러스터의 로그 수집](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) * [ElasticSearch를 서비스 패브릭 응용 프로그램 추적 저장소로 사용](service-fabric-diagnostic-how-to-use-elasticsearch.md)

<!---HONumber=Nov15_HO4-->