---
title: ArrInspector 검사 도구
description: ArrInspector 도구의 사용자 설명서
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723828"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 검사 도구

ArrInspector는 실행 중인 Azure 원격 렌더링 세션을 검사하는 데 사용되는 웹 기반 도구입니다. 이는 디버깅을 위해 사용되며, 렌더링되는 장면의 구조를 검사하고 로그 메시지를 표시하며 서버에서 라이브 성능을 모니터링하기 위한 도구입니다.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>ArrInspector에 연결

ARR 서버의 호스트 이름(`mixedreality.azure.com`으로 끝남)을 가져온 후에는 [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)를 사용하여 연결합니다. 이 함수는 애플리케이션이 실행되는 디바이스에 대한 `StartArrInspector.html`을 만듭니다. ArrInspector를 시작하려면 PC에서 브라우저(Edge, Firefox, 크롬)를 사용하여 해당 파일을 엽니다. 이 파일은 24시간 동안만 유효합니다.

`ConnectToArrInspectorAsync`를 호출하는 앱이 PC에서 이미 실행 중인 경우

* Unity 통합을 사용 중이면 자동으로 시작될 수 있습니다.
* 그러지 않으면 *사용자 폴더\\LocalAppData\\[your_app]\\AC\\Temp* 에서 파일을 찾을 수 있습니다.

앱이 HoloLens에서 실행 중인 경우

1. [Windows 장치 포털](/windows/mixed-reality/using-the-windows-device-portal)을 사용하여 HoloLens에 액세스합니다.
1. *시스템 > 파일 탐색기* 로 이동합니다.
1. *사용자 폴더\\LocalAppData\\[your_app]\\AC\\Temp* 로 이동합니다.
1. *StartArrInspector.html* 을 PC에 저장합니다.
1. *StartArrInspector.html* 을 열어 세션의 ArrInspector를 로드합니다.

## <a name="the-performance-panel"></a>성능 패널

![성능 패널](./media/performance-panel.png)

이 패널에는 서버에서 노출하는 프레임별 성능 값의 그래프가 모두 표시됩니다. 현재, 값에는 프레임 시간, FPS, CPU 및 메모리 사용량 외에도 전체 RAM 사용량, 개체 수 등의 메모리 통계가 포함됩니다.

이러한 매개 변수 중 하나를 시각화하려면 **새로 추가** 단추를 클릭하고 대화 상자에 표시된 사용 가능한 값 중 하나를 선택합니다. 이 작업을 수행하면 새 스크롤 차트가 패널에 추가되고 값이 실시간으로 추적됩니다. 오른쪽에서 *최소*, *최대*, *현재* 값을 볼 수 있습니다.

마우스로 콘텐츠를 끌어서 그래프를 이동할 수 있지만 가로 이동은 ArrInspector가 일시 중지된 상태일 때만 가능합니다.

CTRL 키를 누른 상태로 끌면 확대/축소할 수 있습니다. 가로 확대/축소는 아래쪽의 슬라이더를 사용하여 제어할 수도 있습니다.

세로 범위는 현재 표시된 값에 따라 기본적으로 계산되며 최솟값 및 최댓값은 오른쪽의 텍스트 상자에 표시됩니다. 텍스트 상자에 직접 입력하거나 이동/확대/축소를 사용하여 값을 수동으로 설정하는 경우 그래프는 해당 값을 사용합니다. 자동 세로 프레이밍을 복원하려면 오른쪽 위 모서리에 있는 아이콘을 클릭합니다.

![세로 범위](./media/vertical-range.png)

## <a name="the-log-panel"></a>로그 패널

![로그 패널](./media/log-panel.png)

로그 패널에는 서버 쪽에서 생성된 로그 메시지의 목록이 표시됩니다. 연결 시 최대 200개의 이전 로그 메시지를 표시하고 발생하는 새 로그 메시지를 출력합니다.

맨 위에 있는 단추를 사용하여 로그 유형인 `[Error/Warning/Info/Debug]`에 따라 목록을 필터링할 수 있습니다.
![로그 필터 단추](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>타이밍 데이터 캡처 패널

![타이밍 데이터 캡처](./media/timing-data-capture.png)

이 패널은 서버의 타이밍 정보를 캡처하고 다운로드하는 데 사용됩니다. 이 파일은 [크롬 추적 JSON 형식](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)을 사용합니다. 데이터를 검사하려면 `Chrome://tracing` URL로 크롬을 열고 다운로드한 파일을 페이지로 끌어서 놓습니다. 타이밍 데이터는 고정 크기 링 버퍼에서 지속적으로 수집됩니다. 기록될 경우 캡처는 바로 이전(몇 초에서 몇 분 전)에 대한 정보만 포함합니다.

## <a name="the-scene-inspection-panel"></a>장면 검사 패널

![장면 검사 패널](./media/scene-inspection-panel.png)

이 패널에는 렌더링된 장면의 구조가 표시됩니다. 개체 계층 구조가 왼쪽에 있고 선택한 개체의 내용이 오른쪽에 있습니다. 패널은 읽기 전용이며 실시간으로 업데이트됩니다.

## <a name="the-vm-debug-information-panel"></a>VM 디버그 정보 패널

![VM 디버그 정보 패널](./media/state-debugger-panel.png)

이 패널은 몇 가지 디버그 기능을 제공합니다.

### <a name="restart-service"></a>서비스를 다시 시작

**서비스 다시 시작** 단추는 ArrInspector가 연결된 가상 머신에서 런타임을 다시 시작합니다. 연결된 모든 클라이언트는 연결이 끊어지며, 다시 시작된 서비스에 연결하려면 ArrInspector 페이지를 다시 로드해야 합니다.

### <a name="collect-debug-information"></a>디버그 정보 수집

**VM에 대한 디버그 정보 수집** 단추를 클릭하면 ARR 인스턴스를 트리거하여 VM의 디버그 정보를 수집하는 데 사용할 수 있는 대화 상자가 열립니다.

![VM 디버그 정보 대화 상자](./media/state-debugger-dialog.png)

디버그 정보는 Azure Remote Rendering 팀이 실행 중인 ARR 인스턴스에서 발생하는 모든 문제를 분석하는 데 도움이 됩니다. 이 대화 상자에는 문제를 재현하는 단계와 같은 추가 세부 정보를 제공하는 텍스트 필드가 있습니다.

**수집 시작** 단추를 클릭하면 대화 상자가 닫히고 수집 프로세스가 시작됩니다. VM에 대한 정보를 수집하는 데 몇 분 정도 걸릴 수 있습니다.

![VM 디버그 정보 수집 진행 중](./media/state-debugger-panel-in-progress.png)

수집이 완료되면 ArrInspector 창에서 알림을 받게 됩니다. 이 알림에는 이 특정 컬렉션을 식별하는 ID가 포함되어 있습니다. Azure Remote Rendering 팀에 전달할 수 있도록 이 ID를 저장해야 합니다.

![VM 디버그 정보 수집 성공](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> VM 디버그 정보를 다운로드하거나 해당 정보에 액세스하지 못할 수 있습니다. Azure Remote Rendering 팀만 수집된 데이터에 액세스할 수 있습니다. 표시되는 문제를 조사하려면 Microsoft에 문의하여 컬렉션 ID를 보내야 합니다.

## <a name="pause-mode"></a>일시 중지 모드

오른쪽 위 모서리에서 스위치를 사용하면 패널의 라이브 업데이트를 일시 중지할 수 있습니다. 이 모드는 특정 상태를 신중하게 검사하는 데 유용할 수 있습니다.

![일시 중지 모드](./media/pause-mode.png)

라이브 업데이트를 다시 사용하도록 설정하면 모든 패널이 초기화됩니다.