---
title: ArrInspector 검사 도구
description: ArrInspector 도구의 사용자 설명서
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 5ee9d39db130c76f683ca1f4d1ff64f9ed86d97e
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204744"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 검사 도구

ArrInspector는 실행 중인 Azure 원격 렌더링 세션을 검사 하는 데 사용 되는 웹 기반 도구입니다. 이는 디버깅을 위해 사용 되며, 렌더링 되는 장면의 구조를 검사 하 고, 로그 메시지를 표시 하 고, 서버에서 라이브 성능을 모니터링 하는 데 사용 됩니다.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>ArrInspector에 연결

ARR 서버의 호스트 이름 (종료)을 가져온 후에는 `mixedreality.azure.com` [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)를 사용 하 여 연결 합니다. 이 함수는 `StartArrInspector.html` 응용 프로그램이 실행 되는 장치에를 만듭니다. ArrInspector를 시작 하려면 PC에서 브라우저 (Edge, Firefox 또는 Chrome)를 사용 하 여 해당 파일을 엽니다. 파일은 24 시간 동안만 유효 합니다.

를 호출 하는 앱 `ConnectToArrInspectorAsync` 이 PC에서 이미 실행 중인 경우:

* Unity 통합을 사용 하는 경우 자동으로 시작 될 수 있습니다.
* 그렇지 않으면 *사용자 폴더 \\ LocalAppData \\ [your_app] \\ AC \\ 온도*에서 파일을 찾을 수 있습니다.

앱이 HoloLens에서 실행 되는 경우:

1. [Windows 장치 포털](/windows/mixed-reality/using-the-windows-device-portal)을 사용 하 여 HoloLens에 액세스 합니다.
1. *시스템 > 파일 탐색기*로 이동 합니다.
1. *사용자 폴더 \\ LocalAppData \\ [your_app] \\ AC \\ 온도*로 이동 합니다.
1. *StartArrInspector.html* 을 PC에 저장 합니다.
1. *StartArrInspector.html* 을 열어 세션의 ArrInspector를 로드 합니다.

## <a name="the-performance-panel"></a>성능 패널

![성능 패널](./media/performance-panel.png)

이 패널에는 서버에서 노출 하는 프레임별 성능 값의 그래프가 모두 표시 됩니다. 값에는 현재 프레임 시간, FPS, CPU 및 메모리 사용량, 전체 RAM 사용, 개체 수 등의 메모리 통계가 포함 됩니다.

이러한 매개 변수 중 하나를 시각화 하려면 **새로 추가** 단추를 클릭 하 고 대화 상자에 표시 된 사용 가능한 값 중 하나를 선택 합니다. 이 작업을 수행 하면 새 스크롤 차트가 패널에 추가 되 고 값이 실시간으로 추적 됩니다. 오른쪽에서 *최소*, *최대* 및 *현재* 값을 볼 수 있습니다.

마우스를 사용 하 여 그래프를 이동할 수 있지만, 가로 패닝은 ArrInspector가 일시 중지 된 상태일 때만 가능 합니다.

CTRL 키를 누른 상태로 끌면 확대/축소할 수 있습니다. 가로 확대/축소는 아래쪽의 슬라이더를 사용 하 여 제어할 수도 있습니다.

세로 범위는 현재 표시 된 값에 따라 기본적으로 계산 되며 최소 및 최대 값은 오른쪽의 텍스트 상자에 표시 됩니다. 텍스트 상자에 직접 입력 하거나 이동/확대/축소를 사용 하 여 값을 수동으로 설정 하는 경우 그래프는 이러한 값을 사용 합니다. 자동 세로 프레이밍을 복원 하려면 오른쪽 위 모퉁이에 있는 아이콘을 클릭 합니다.

![세로 범위](./media/vertical-range.png)

## <a name="the-log-panel"></a>로그 패널

![로그 패널](./media/log-panel.png)

로그 패널에는 서버 쪽에 생성 된 로그 메시지의 목록이 표시 됩니다. 연결 시 최대 200 이전 로그 메시지를 표시 하 고 발생 하는 새 로그 메시지를 인쇄 합니다.

`[Error/Warning/Info/Debug]`맨 위에 있는 단추를 사용 하 여 로그 유형에 따라 목록을 필터링 할 수 있습니다.
![로그 필터 단추](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>타이밍 데이터 캡처 패널

![타이밍 데이터 캡처](./media/timing-data-capture.png)

이 패널은 서버에서 타이밍 정보를 캡처하고 다운로드 하는 데 사용 됩니다. 이 파일은 [Chrome 추적 JSON 형식을](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)사용 합니다. 데이터를 검사 하려면 URL에서 Chrome을 열고 `Chrome://tracing` 다운로드 한 파일을 페이지로 끌어서 놓습니다. 타이밍 데이터는 고정 크기 링 버퍼에서 지속적으로 수집 됩니다. 기록 되 면 캡처는 바로 이전에 대 한 정보만 포함 합니다. 즉, 몇 초에서 몇 분 정도 걸릴 수 있습니다.

## <a name="the-scene-inspection-panel"></a>장면 검사 패널

![장면 검사 패널](./media/scene-inspection-panel.png)

이 패널에는 렌더링 된 장면의 구조가 표시 됩니다. 개체 계층 구조가 왼쪽에 있고 선택한 개체의 내용이 오른쪽에 있습니다. 패널은 읽기 전용 이며 실시간으로 업데이트 됩니다.

## <a name="the-vm-debug-information-panel"></a>VM 디버그 정보 패널

![VM 디버그 정보 패널](./media/state-debugger-panel.png)

이 패널은 몇 가지 디버그 기능을 제공 합니다.

### <a name="restart-service"></a>서비스를 다시 시작

**서비스 다시 시작** 단추는 arrInspector가 연결 된 가상 컴퓨터에서 런타임을 다시 시작 합니다. 연결 된 모든 클라이언트는 연결이 끊어지고 다시 시작 된 서비스에 연결 하려면 arrInspector 페이지를 다시 로드 해야 합니다.

### <a name="collect-debug-information"></a>디버그 정보 수집

**Vm에 대 한 디버그 정보 수집** 단추를 클릭 하면 ARR 인스턴스를 트리거하여 vm의 디버그 정보를 수집 하는 데 사용할 수 있는 대화 상자가 열립니다.

![VM 디버그 정보 대화 상자](./media/state-debugger-dialog.png)

디버그 정보는 Azure 원격 렌더링 팀이 실행 중인 ARR 인스턴스에서 발생 하는 모든 문제를 분석 하는 데 도움이 됩니다. 이 대화 상자에는 문제를 재현 하는 단계와 같은 추가 세부 정보를 제공 하는 텍스트 필드가 있습니다.

**수집 시작** 단추를 클릭 하면 대화 상자가 닫히고 수집 프로세스가 시작 됩니다. VM에 대 한 정보를 수집 하는 데 몇 분 정도 걸릴 수 있습니다.

![VM 디버그 정보 수집 진행 중](./media/state-debugger-panel-in-progress.png)

수집이 완료 되 면 ArrInspector 창에서 알림을 받게 됩니다. 이 알림에는이 특정 컬렉션을 식별 하는 ID가 포함 되어 있습니다. Azure 원격 렌더링 팀에 전달 하려면이 ID를 저장 해야 합니다.

![VM 디버그 정보 수집 성공](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> VM 디버그 정보를 다운로드 하거나 액세스 하지 못할 수 있습니다. Azure 원격 렌더링 팀만 수집 된 데이터에 액세스할 수 있습니다. 표시 되는 문제를 조사 하려면 microsoft에 문의 하 여 컬렉션 ID를 보내야 합니다.

## <a name="pause-mode"></a>일시 중지 모드

오른쪽 위 모서리에서 스위치를 사용 하면 패널의 라이브 업데이트를 일시 중지할 수 있습니다. 이 모드는 특정 상태를 신중 하 게 검사 하는 데 유용할 수 있습니다.

![일시 중지 모드](./media/pause-mode.png)

라이브 업데이트를 다시 사용 하도록 설정 하면 모든 패널이 다시 설정 됩니다.

## <a name="host-configuration"></a>호스트 구성

기본적으로이 도구는 ArrInspector를 제공 하는 동일한 호스트에서 실행 되는 ARR 서버에 연결 합니다. 그러나 도구 포트가 열려 있는 ARR 인스턴스를 실행 하 고 있다고 가정 하 고 다른 서버를 검사 하도록 구성할 수 있습니다.

이렇게 하려면 머리글 표시줄의 왼쪽에 있는 주 메뉴에 액세스 하 고 *호스트 구성*을 선택 합니다. **새 호스트 추가**를 클릭 하 고 이름 및 호스트 이름을 입력 합니다. *호스트 이름* 에는에서 끝나는 호스트 이름만 사용 `.mixedreality.azure.com` 하 고 `http://` 또는 포트는 포함 하지 마세요.

![호스트 구성](./media/host-configuration.png)

한 호스트에서 다른 호스트로 신속 하 게 전환 하려면 오른쪽 위에 있는 드롭다운을 사용 합니다.

![호스트 콤보](./media/host-switch-combo.png)

호스트 목록은 브라우저 로컬 저장소에 저장 되므로 동일한 브라우저를 다시 열면 유지 됩니다.