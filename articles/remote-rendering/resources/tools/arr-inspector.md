---
title: ArrInspector 검사 도구
description: ArrInspector 도구의 사용자 설명서
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680077"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 검사 도구

ArrInspector는 실행 중인 Azure 원격 렌더링 세션을 검사하는 데 사용되는 웹 기반 도구입니다. 디버깅, 렌더링 중인 장면의 구조를 검사하고, 로그 메시지를 표시하고, 서버에서 라이브 성능을 모니터링하는 데 사용됩니다.

![아르스피터](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>ArrInspector에 연결

ARR 서버의 호스트 이름(종료)을 `mixedreality.azure.com`얻으면 [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)를 사용하여 연결합니다. 이 함수는 `StartArrInspector.html` 응용 프로그램이 실행 중인 장치에서 를 만듭니다. ArrInspector를 실행하려면 PC에서 브라우저(가장자리, 파이어폭스 또는 크롬)를 사용하여 해당 파일을 엽니다. 이 파일은 24시간 동안만 유효합니다.

호출하는 `ConnectToArrInspectorAsync` 앱이 이미 PC에서 실행 중인 경우:

* Unity 통합을 사용하는 경우 자동으로 시작될 수 있습니다.
* 그렇지 않으면 사용자 폴더 *LocalAppData\\\\\\[your_app]\\AC Temp에서*파일을 찾을 수 있습니다.

앱이 HoloLens에서 실행중인 경우:

1. [Windows 장치 포털을](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)사용하여 HoloLens에 액세스합니다.
1. 시스템 *> 파일 탐색기로*이동합니다.
1. 사용자 *폴더\\LocalAppData\\[your_app]\\\\AC Temp로*이동합니다.
1. *당신의 PC에 StartArrInspector.html을* 저장합니다.
1. *시작ArrInspector.html을* 열어 세션의 ArrInspector를 로드합니다.

## <a name="the-performance-panel"></a>성능 패널

![성능 패널](./media/performance-panel.png)

이 패널에서는 서버에서 노출하는 모든 프레임별 성능 값의 그래프를 보여 주며, 이 패널에서는 현재 값에는 프레임 시간, FPS, CPU 및 메모리 사용량, 전체 RAM 사용량, 개체 수 등과 같은 메모리 통계가 포함됩니다.

이러한 매개 변수 중 하나를 시각화하려면 **새 추가** 단추를 클릭하고 대화 상자에 표시된 사용 가능한 값 중 하나를 선택합니다. 이 작업은 실시간으로 값을 추적, 패널에 새로운 스크롤 차트를 추가합니다. 오른쪽에서 *최소*값, *최대* 값 및 *현재* 값을 볼 수 있습니다.

그래프를 마우스로 드래그하여 그래프를 이동할 수 있지만 ArrInspector가 일시 중지된 상태에 있는 경우에만 가로로 패닝이 가능합니다.

드래그하는 동안 CTRL을 잡고, 당신은 확대 할 수 있습니다. 아래쪽의 슬라이더로 가로 줌을 제어할 수도 있습니다.

세로 범위는 기본적으로 현재 표시된 값을 기반으로 계산되며, 최소 및 최대 값은 오른쪽의 텍스트 상자에 표시됩니다. 값이 텍스트 상자에 직접 입력하거나 이동/확대/축소하여 수동으로 설정하면 그래프에서 해당 값을 사용합니다. 자동 수직 프레임을 복원하려면 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다.

![수직 범위](./media/vertical-range.png)

## <a name="the-log-panel"></a>로그 패널

![로그 패널](./media/log-panel.png)

로그 패널에는 서버 측에서 생성된 로그 메시지 목록이 표시됩니다. 연결시 최대 200개의 이전 로그 메시지가 표시되며 새 로그 메시지가 인쇄됩니다.

상단의 단추를 사용하여 로그 `[Error/Warning/Info/Debug]` 유형에 따라 목록을 필터링할 수 있습니다.
![로그 필터 버튼](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>타이밍 데이터 캡처 패널

![타이밍 데이터 캡처](./media/timing-data-capture.png)

이 패널은 서버에서 타이밍 정보를 캡처하고 다운로드하는 데 사용됩니다. 이 파일은 [크롬 추적 JSON 형식을](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)사용합니다. 데이터를 검사하려면 URL에서 `Chrome://tracing` Chrome을 열고 다운로드한 파일을 페이지로 끌어서 놓습니다. 타이밍 데이터는 고정 크기 링 버퍼에서 지속적으로 수집됩니다. 기록된 경우 캡처에는 바로 과거에 대한 정보만 포함되므로 몇 초에서 몇 분까지 표시됩니다.

## <a name="the-scene-inspection-panel"></a>장면 검사 패널

![장면 검사 패널](./media/scene-inspection-panel.png)

이 패널에는 렌더링된 장면의 구조가 표시됩니다. 개체 계층 구조는 왼쪽에, 선택한 개체의 콘텐츠는 오른쪽에 있습니다. 패널은 읽기 전용이며 실시간으로 업데이트됩니다.

## <a name="the-vm-debug-information-panel"></a>VM 디버그 정보 패널

![VM 디버그 정보 패널](./media/state-debugger-panel.png)

이 패널에서는 몇 가지 디버그 기능을 제공합니다.

### <a name="restart-service"></a>서비스를 다시 시작

**서비스 다시 시작** 단추는 arrInspector가 연결된 가상 컴퓨터에서 런타임을 다시 시작합니다. 연결된 클라이언트의 연결이 끊어지고 다시 시작된 서비스에 연결하려면 arrInspector 페이지를 다시 로드해야 합니다.

### <a name="collect-debug-information"></a>디버그 정보 수집

**VM에 대한 디버그 정보 수집** 단추는 ARR 인스턴스를 트리거하여 VM에서 디버그 정보를 수집할 수 있는 대화 상자를 엽니다.

![VM 디버그 정보 대화 상자](./media/state-debugger-dialog.png)

디버그 정보는 Azure 원격 렌더링 팀이 실행 중인 ARR 인스턴스에서 발생하는 문제를 분석하는 데 도움이 됩니다. 대화 상자에는 문제를 재현하는 단계와 같은 추가 세부 정보를 제공하는 텍스트 필드가 있습니다.

**수집 시작** 단추를 클릭하면 대화 상자가 닫히고 수집 프로세스가 시작됩니다. VM에 대한 정보를 수집하는 데 몇 분 정도 걸릴 수 있습니다.

![진행 중인 VM 디버그 정보 수집](./media/state-debugger-panel-in-progress.png)

컬렉션이 완료되면 ArrInspector 창에서 알림을 받게 됩니다. 이 알림에는 이 특정 컬렉션을 식별하는 ID가 포함되어 있습니다. 이 ID를 저장하여 Azure 원격 렌더링 팀에 전달해야 합니다.

![VM 디버그 정보 수집 성공](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> VM 디버그 정보를 다운로드하거나 액세스할 수 없습니다. Azure 원격 렌더링 팀만 수집된 데이터에 액세스할 수 있습니다. 당신은 저희에게 연락하고 당신이 보고있는 문제를 조사하기 위해, 함께 수집 ID를 보내야합니다.

## <a name="pause-mode"></a>일시 중지 모드

오른쪽 상단 모서리에서 스위치를 사용하면 패널의 라이브 업데이트를 일시 중지할 수 있습니다. 이 모드는 특정 상태를 주의 깊게 검사하는 데 유용할 수 있습니다.

![일시 중지 모드](./media/pause-mode.png)

라이브 업데이트를 다시 활성화하면 모든 패널이 재설정됩니다.

## <a name="host-configuration"></a>호스트 구성

기본적으로 도구는 ArrInspector를 제공하는 동일한 호스트에서 실행중인 ARR 서버에 연결됩니다. 그러나 툴링 포트가 열려 있는 ARR 인스턴스를 실행한다고 가정하여 다른 서버를 검사하도록 구성할 수 있습니다.

이렇게 하려면 헤더 모음 왼쪽에 있는 기본 메뉴에 액세스하고 *호스트 구성을*선택합니다. **새 호스트 추가를**클릭하고 이름과 호스트 이름을 입력합니다. *hostname의* 경우 `.mixedreality.azure.com`에서 끝나는 호스트 이름만 `http://` 사용하십시오.

![호스트 구성](./media/host-configuration.png)

한 호스트에서 다른 호스트로 빠르게 전환하려면 오른쪽 상단의 드롭다운을 사용합니다.

![호스트 콤보](./media/host-switch-combo.png)

호스트 목록은 브라우저 로컬 저장소에 저장되므로 동일한 브라우저를 다시 열 때 유지됩니다.
