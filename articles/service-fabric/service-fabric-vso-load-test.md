<properties
    pageTitle="Visual Studio Team Services를 사용하여 응용 프로그램 부하 테스트 | Microsoft Azure"
    description="Visual Studio Team Services를 사용하여 Azure 서비스 패브릭 응용 프로그램에 스트레스 테스트를 실행하는 방법을 알아봅니다."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Visual Studio Team Services를 사용하여 응용 프로그램 부하 테스트

이 문서는 Visual Studio 부하 테스트 기능을 사용하여 응용 프로그램에 스트레스 테스트를 실행하는 방법을 보여줍니다. 서비스 패브릭 상태 저장 서비스 백 엔드 및 상태 비저장 서비스 웹 프런트 엔드가 사용됩니다. 여기에 사용되는 예제 응용 프로그램은 항공기 위치 시뮬레이터입니다. 사용자는 항공기 ID, 출발 위치 및 도착 위치를 제공합니다. 응용 프로그램의 백 엔드는 요청을 처리하고 프런트 엔드는 지도의 조건에 일치하는 항공기를 표시합니다.

다음 다이어그램은 테스트를 수행할 서비스 패브릭 응용 프로그램을 보여 줍니다.

![][0]

## 필수 조건
시작하기 전에 다음을 수행해야 합니다.

- VSTS(Visual Studio Team Services) 계정을 가져옵니다. [Visual Studio Team Services](https://www.visualstudio.com)에서 무료로 계정을 등록할 수 있습니다.
- Visual Studio 2013 또는 Visual Studio 2015를 확보하여 설치합니다. 이 문서는 Visual Studio 2015 Enterprise Edition을 사용하지만 Visual Studio 2013 및 기타 버전도 유사하게 작동합니다.
- 스테이징 환경에 응용 프로그램을 배포합니다. 자세한 내용은 [Visual Studio를 사용하여 원격 클러스터에 응용 프로그램을 배포하는 방법(영문)](service-fabric-publish-app-remote-cluster.md)을 참조하세요.
- 응용 프로그램 사용 패턴을 이해합니다. 이 정보는 부하 패턴을 시뮬레이션하는 데 사용됩니다.
- 부하 테스트의 목표를 이해합니다. 이것은 부하 테스트 결과를 해석하고 분석하는 데 도움이 됩니다.

## 웹 성능 및 부하 테스트 프로젝트 만들기 및 실행

### 웹 성능 및 부하 테스트 만들기

1. Visual Studio 2015를 엽니다. 메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 **새 프로젝트** 대화 상자를 엽니다.

1. **Visual C#** 노드를 확장하고 **테스트** > **웹 성능 및 부하 테스트 프로젝트**를 선택합니다. 프로젝트 이름을 부여하고 **확인** 단추를 선택합니다.

    ![][1]

    새로운 웹 성능 및 부하 테스트 프로젝트가 표시됩니다.

    ![][2]

### 웹 성능 테스트 기록

1. .webtest 프로젝트를 엽니다.

1. **기록 추가** 아이콘을 선택하여 사용자 브라우저에서 기록 세션을 시작합니다.

    ![][3]

    ![][4]

1. 서비스 패브릭 응용 프로그램으로 이동합니다. 기록 패널에 웹 요청이 표시됩니다.

    ![][5]

1. 사용자가 수행할 것으로 예상하는 작업 시퀀스를 수행합니다. 이 작업은 부하를 생성하는 패턴으로 사용됩니다.

1. 완료되면 **중지** 단추를 선택하여 기록을 중지합니다.

    ![][6]

    Visual Studio의 .webtest 프로젝트가 일련의 요청으로 캡처되었습니다. 동적 매개 변수가 자동으로 대체됩니다. 이 때, 테스트 시나리오에 속하지 않는 중복된 종속 요청 또는 여분의 요청을 제거할 수 있습니다.

1. 프로젝트를 저장한 후 **테스트 실행** 명령을 선택하여 로컬에서 웹 성능 테스트를 실행하고 모든 것이 올바르게 작동하는지 확인합니다.

    ![][7]

### 웹 성능 테스트 매개 변수화

웹 성능 테스트를 코딩된 웹 성능 테스트로 변환한 후 코드를 편집하여 웹 성능 테스트를 매개 변수화할 수 있습니다. 또는 테스트가 데이터를 통해 반복되도록 웹 성능 테스트를 데이터 목록에 바인딩할 수 있습니다. 웹 성능 테스트를 코딩된 테스트로 변환하는 방법에 대한 내용은 [코딩된 웹 성능 테스트 생성 및 실행](https://msdn.microsoft.com/library/ms182552.aspx)을 웹 성능 테스트에 데이터를 바인딩하는 방법에 대한 정보는 [웹 성능 테스트에 데이터 소스 추가](https://msdn.microsoft.com/library/ms243142.aspx)를 참조하세요.

이 예제에서는 항공기 ID를 생성된 GUID로 대체하고 여러 지역에 항공편을 보내는 요청을 추가할 수 있도록 웹 성능 테스트를 코딩된 테스트로 변환합니다.

### 부하 테스트 프로젝트 만들기

부하 테스트 프로젝트는 추가적으로 지정되는 부하 테스트 설정과 함께 웹 성능 테스트 및 단위 테스트로 설명되는 하나 이상의 시나리오로 구성됩니다. 다음 단계는 부하 테스트 프로젝트를 만드는 방법을 보여줍니다.

1. 웹 성능 및 부하 테스트 프로젝트의 바로 가기 메뉴에서 **추가** > **부하 테스트**를 선택합니다. **부하 테스트** 마법사에서 **다음** 단추를 선택하여 테스트 설정을 구성합니다.

1. **부하 패턴** 섹션에서 일정 사용자 부하를 원하는지 사용자 몇 명으로 시작하여 시간이 지나면서 사용자를 늘리는 단계 부하를 원하는지 선택합니다.

    사용자 부하에 대한 예측이 믿을만하고 현재 시스템의 성능을 보려면 **일정 부하**를 선택합니다. 테스트의 목표가 다양한 양의 부하에 대해 시스템 성능이 일관적으로 수행되는지를 파악하는 것이라면 **단계 부하**를 선택합니다.

1. **테스트 조합** 섹션에서 **추가** 단추를 선택한 후 부하 테스트에 포함할 테스트를 선택합니다. **분포** 열을 사용하여 각 테스트에 실행할 총 테스트의 백분율을 지정할 수 있습니다.

1. **실행 설정** 섹션에서 부하 테스트 지속 시간을 지정합니다.

    >[AZURE.NOTE]**테스트 반복** 옵션은 Visual Studio를 사용하여 로컬에서 부하 테스트를 실행하는 경우에만 사용할 수 있습니다.


1. **실행 설정**의 **위치** 섹션에서 부하 테스트 요청이 생성되는 위치를 지정합니다. 마법사가 Team Services 계정에 로그인하라는 메시지를 표시할 수 있습니다. Team Services 계정을 사용하여 로그인한 후 지리적 위치를 선택합니다. 완료되면 **마침** 단추를 선택합니다.

1. 부하 테스트를 만든 후에 .loadtest 프로젝트를 열어서 현재 실행 설정을 선택합니다(예: **실행 설정** > **실행 설정1 [활성]**). 그러면 **속성** 창에 실행 설정이 열립니다.

1. **실행 설정** 속성 창의 **결과** 섹션에, **타이밍 정보 저장소** 설정의 기본 값이 **없음**으로 나타납니다. 부하 테스트 결과에 대해 자세한 정보를 보려면 이 값을 **모든 개인 정보**로 변경합니다. Visual Studio Team Services에 연결하여 부하 테스트를 실행하는 방법을 자세히 보려면 [부하 테스트](https://www.visualstudio.com/load-testing.aspx)를 참조하세요.

### Visual Studio Team Services를 사용하여 부하 테스트 실행

**부하 테스트 실행** 명령을 선택하여 테스트 실행을 시작합니다.

![][8]

## 부하 테스트 보기 및 분석

부하 테스트가 진행됨에 따라서 성능 정보가 그래프로 표시됩니다. 다음과 유사한 그래프가 표시됩니다.

![][9]

1. 페이지 맨 위 근처에서 **보고서 다운로드** 링크를 선택합니다. 보고서를 다운로드한 후에 **보고서 보기** 단추를 선택합니다.

    **그래프** 탭에 다양한 성능 카운터의 그래프가 표시됩니다. **요약** 탭에 전반적인 테스트 결과가 표시됩니다. **테이블** 탭에 성공한 테스트와 실패한 테스트의 총 수가 표시됩니다.

1. **테스트** > **실패** 및 **오류** > **개수** 열에서 숫자 링크를 선택하여 오류 정보를 확인합니다.

    **세부 정보** 탭에 실패한 요청의 가상 사용자 및 테스트 시나리오 정보가 표시됩니다. 이 데이터는 가상 테스트에 여러 시나리오가 포함된 경우에 유용합니다.

부하 테스트 결과를 보는 방법에 대한 자세한 내용은 [부하 테스트 분석기의 그래프 보기에서 부하 테스트 결과 분석(영문)](https://www.visualstudio.com/load-testing.aspx)을 참조하세요.

## 부하 테스트 자동화

Visual Studio Team Services 부하 테스트에는 Team Services 계정으로 부하 테스트를 관리하고 결과를 분석할 수 있도록 하는 API가 제공됩니다. 자세한 내용은 [클라우드 부하 테스트 REST API(영문)](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx)를 참조하세요.

## 다음 단계
- [로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png

<!---HONumber=AcomDC_1217_2015-->
