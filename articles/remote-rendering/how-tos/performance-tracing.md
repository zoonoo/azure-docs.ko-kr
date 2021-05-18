---
title: 클라이언트 쪽 성능 추적 만들기
description: WPF를 사용한 클라이언트 쪽 성능 프로파일링에 대한 모범 사례
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92204098"
---
# <a name="create-client-side-performance-traces"></a>클라이언트 쪽 성능 추적 만들기

Azure Remote Rendering의 성능이 원하는 만큼 적절하지 않을 수 있는 많은 이유가 있습니다. 클라우드 서버의 순수 렌더링 성능 외에 특히 네트워크 연결의 품질은 환경에 상당한 영향을 미칩니다. 서버 성능을 프로파일링하려면 [서버 쪽 성능 쿼리](../overview/features/performance-queries.md) 챕터를 참조하세요.

이 챕터에서는 *:::no-loc text="performance traces":::* 를 통해 잠재적 클라이언트 쪽 병목 상태를 식별하는 방법을 중점적으로 설명합니다.

## <a name="getting-started"></a>시작

Windows :::no-loc text="performance tracing"::: 기능을 처음 접하는 경우 이 섹션에서 가장 기본적인 용어와 애플리케이션을 시작하는 방법을 설명합니다.

### <a name="installation"></a>설치

ARR을 사용하여 추적을 수행하는 데 사용되는 애플리케이션은 모든 Windows 개발에 사용할 수 있는 범용 도구입니다. [Windows 성능 도구 키트](/windows-hardware/test/wpt/)를 통해 제공됩니다. 이 도구 키트를 얻으려면 [Windows 평가 및 배포 키트](/windows-hardware/get-started/adk-install)를 다운로드하세요.

### <a name="terminology"></a>용어

성능 추적에 대한 정보를 검색하는 경우 불가피하게 다양한 용어를 접하게 됩니다. 가장 중요한 용어는 다음과 같습니다.

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** 는 Windows용 이벤트 추적([**E** vent **T** racing for **W** indows](/windows/win32/etw/about-event-tracing))을 나타냅니다. 이는 간단히 Windows에 기본 제공되는 효율적인 커널 수준 추적 기능에 중요한 이름입니다. ETW를 지원하는 애플리케이션에서 성능 문제를 추적하는 데 도움이 될 수 있는 작업을 기록하기 때문에 *이벤트* 추적이라고 합니다. 기본적으로 운영 체제는 디스크 액세스, 작업 스위치 등과 같은 항목에 대한 이벤트를 이미 내보냅니다. ARR과 같은 애플리케이션은 사용자 지정 이벤트(예: 삭제된 프레임, 네트워크 지연 등)를 추가로 내보냅니다.

**ETL** 은 이벤트 추적 로깅(**E** vent **T** race **L** ogging)을 나타냅니다. 이는 간단히 추적이 수집(기록)되어 일반적으로 추적 데이터를 저장하는 파일에 대한 파일 확장명으로 사용됨을 의미합니다. 따라서 추적을 수행할 때 일반적으로 나중에 \*.etl 파일을 보유하게 됩니다.

**WPR** 은 Windows 성능 레코더([**W** indows **P** erformance **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder))를 나타내며 이벤트 추적 기록을 시작하고 중지하는 애플리케이션의 이름입니다. WPR은 로깅할 정확한 이벤트를 구성하는 프로필 파일(\*.wprp)을 사용합니다. 이러한 `wprp` 파일은 ARR SDK와 함께 제공됩니다. 데스크톱 PC에서 추적을 수행할 때 WPR를 직접 시작할 수 있습니다. HoloLens에서 추적을 수행할 때는 일반적으로 웹 인터페이스를 통해 수행합니다.

**WPA** 는 Windows 성능 분석기([**W** indows **P** erformance **A** nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer))를 나타내며 \*.etl 파일을 열고 성능 문제를 식별하기 위해 데이터를 확인하는 데 사용되는 GUI 애플리케이션의 이름입니다. WPA를 사용하면 다양한 조건에 따라 데이터를 정렬하고, 여러 가지 방식으로 데이터를 표시하고, 세부 정보를 확인하고, 정보를 상호 연결할 수 있습니다

ETL 추적은 모든 Windows 디바이스(로컬 PC, HoloLens, 클라우드 서버 등)에서 만들 수 있지만 일반적으로 디스크에 저장되고 데스크톱 PC에서 WPA를 사용하여 분석됩니다. ETL 파일을 다른 개발자가 확인할 수 있도록 전송할 수 있습니다. 그러나 파일 경로 및 IP 주소와 같은 중요한 정보는 ETL 추적에서 캡처될 수 있습니다. ETW는 추적을 기록하거나 추적을 분석하는 두 가지 방법으로 사용할 수 있습니다. 추적 기록은 매우 간단하며 최소한의 설정으로 가능합니다. 반면 추적 분석은 WPA 도구와 조사 중인 문제에 대한 적절한 이해가 있어야 합니다. WPA 학습에 대한 일반 재질 및 ARR 관련 추적을 해석하는 방법에 대한 지침이 아래에 나와 있습니다.

## <a name="recording-a-trace-on-a-local-pc"></a>로컬 PC에서 추적 기록

ARR 성능 문제를 식별하려면 HoloLens에서 직접 추적을 수행하는 것이 좋습니다. 진정한 성능 특성의 스냅샷을 가져오는 유일한 방법이기 때문입니다. 그러나 특히 HoloLens 성능 제한 없이 추적을 수행하거나 WPA를 사용하는 방법을 알아보고 현실적인 추적이 필요하지 않은 경우에는 다음을 수행합니다.

### <a name="wpr-configuration"></a>WPR 구성

1. *시작 메뉴* 에서 [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder)를 시작합니다.
1. **추가 옵션** 을 확장합니다.
1. **프로필 추가...** 를 클릭합니다.
1. *AzureRemoteRenderingNetworkProfiling.wprp* 파일을 선택합니다. 이 파일은 ARR SDK의 *Tools/ETLProfiles* 에서 찾을 수 있습니다.
   이제 프로필이 *사용자 지정 측정* 아래 WPR에 나열됩니다. 유일하게 사용할 수 있는 프로필인지 확인합니다.
1. *첫 번째 수준 심사* 를 확장합니다.
    * ARR 네트워킹 이벤트의 신속한 추적을 캡처하려면 이 옵션을 **비활성화** 합니다.
    * CPU 또는 메모리 사용량과 같은 다른 시스템 특성과 ARR 네트워크 이벤트를 상호 연결해야 하는 경우 이 옵션을 **활성화** 합니다.
    * 이 옵션을 활성화할 경우 추적의 크기가 수 기가바이트로 커질 수 있으므로 WPA에서 저장하고 여는 데 시간이 오래 걸릴 수 있습니다.

이후 WPR 구성은 다음과 같이 표시됩니다.

![WPR 구성](./media/wpr.png)

### <a name="recording"></a>기록 중

**시작** 을 클릭하여 추적 기록을 시작합니다. 언제든지 기록을 시작하고 중지할 수 있으며 이 작업을 수행하기 전에 애플리케이션을 종료할 필요가 없습니다. 여기서 볼 수 있듯이, ETW는 항상 전체 시스템에 대한 추적을 기록하므로 추적할 애플리케이션을 지정할 필요가 없습니다. `wprp` 파일은 기록할 이벤트 유형을 지정합니다.

**저장** 을 클릭하여 기록을 중지하고 ETL 파일을 저장할 위치를 지정합니다.

이제 WPA에서 직접 열거나 다른 사용자에게 보낼 수 있는 ETL 파일이 생성되었습니다.

## <a name="recording-a-trace-on-a-hololens"></a>HoloLens에서 추적 기록

HoloLens에서 추적을 기록하려면 디바이스를 부팅하고 브라우저에 해당 IP 주소를 입력하여 *디바이스 포털* 을 엽니다.

![디바이스 포털](./media/wpr-hl.png)

1. 왼쪽에서 *성능 > 성능 추적* 으로 이동합니다.
1. **사용자 지정 프로필** 을 선택합니다.
1. **:::no-loc text="Browse...":::**
1. *AzureRemoteRenderingNetworkProfiling.wprp* 파일을 선택합니다. 이 파일은 ARR SDK의 *Tools/ETLProfiles* 에서 찾을 수 있습니다.
1. **추적 시작** 을 클릭합니다.
1. 이제 HoloLens가 추적을 기록합니다. 조사하려는 성능 문제를 트리거해야 합니다. 그런 다음 **추적 중지** 를 클릭합니다.
1. 추적은 웹 페이지의 맨 아래에 나열됩니다. ETL 파일을 다운로드하려면 오른쪽에 있는 디스크 아이콘을 클릭합니다.

이제 WPA에서 직접 열거나 다른 사용자에게 보낼 수 있는 ETL 파일이 생성되었습니다.

## <a name="analyzing-traces-with-wpa"></a>WPA를 사용하여 추적 분석

### <a name="wpa-basics"></a>WPA 기본 사항

Windows 성능 분석기는 ETL 파일을 열고 추적을 검사하는 표준 도구입니다. WPA 작동 방법에 대한 설명은 이 문서의 범위에 포함되지 않습니다. 시작하려면 다음 리소스를 살펴보세요.

* 첫 번째 개요는 [소개 비디오](/windows-hardware/test/wpt/windows-performance-analyzer)를 시청하세요.
* WPA 자체에 일반적인 단계를 설명하는 *시작* 탭이 있습니다. 사용 가능한 항목을 살펴보세요. 특히 "데이터 보기"에서 특정 데이터에 대한 그래프 작성 방법에 대한 간략한 소개를 볼 수 있습니다.
* [이 웹 사이트](https://randomascii.wordpress.com/2015/09/24/etw-central/)에는 유용한 정보가 있지만 일부는 초보자와 관련이 없습니다.

### <a name="graphing-data"></a>그래프 데이터

ARR 추적을 시작하려면 다음 부분을 잘 알고 있어야 합니다.

![성능 그래프](./media/wpa-graph.png)

위의 이미지는 추적 데이터 테이블 및 동일한 데이터를 그래프로 표현하여 보여 줍니다.

아래쪽에 있는 테이블에서 노란색(골드) 표시줄과 파란색 표시줄에 주목합니다. 이러한 표시줄을 끌어 임의의 위치에 배치할 수 있습니다.

**노란색 표시줄의 왼쪽에 있는 모든 열** 은 **키** 로 해석됩니다. 키를 사용하여 왼쪽 위 창에서 트리를 구성할 수 있습니다. 여기에는 "공급자 이름"과 "작업 이름"이라는 두 개의 *키* 열이 있습니다. 따라서 왼쪽 위 창의 트리 구조의 수준은 2단계입니다. 열의 순서를 변경하거나 키 영역에서 열을 추가하거나 제거하면 트리 뷰의 구조가 변경됩니다.

**파란색 표시줄 오른쪽의 열** 은 오른쪽 상단 창의 **그래프 표시** 에 사용됩니다. 대부분의 경우 첫 번째 열만 사용되지만 일부 그래프 모드에서는 데이터 열이 여러 개 필요합니다. 선 그래프가 작동하려면 해당 열에서 *집계 모드* 를 설정해야 합니다. 'Avg' 또는 'Max'를 사용합니다. 픽셀이 여러 이벤트가 있는 범위를 포함하는 경우 집계 모드를 사용하여 지정된 픽셀에서 그래프의 값을 확인합니다. 집계를 'Sum'으로 설정한 다음 확대/축소하여 이 값을 관찰할 수 있습니다.

중간의 열은 특별한 의미가 없습니다.

![이벤트 보기](./media/wpa-event-view.png)

*일반 이벤트 뷰 편집기* 에서 표시할 모든 열, 집계 모드, 정렬 및 키로 사용되는 열, 그래프로 표시할 열을 구성할 수 있습니다. 위의 예에서는 **Field 2** 를 사용하고 Field 3~6은 사용하지 않습니다. Field 2는 일반적으로 ETW 이벤트의 첫 번째 *사용자 지정 데이터* 필드이므로 일부 네트워크 대기 시간 값을 나타내는 ARR "FrameStatistics" 이벤트에 대한 필드입니다. 이 이벤트의 추가 값을 보려면 다른 "Field" 열을 사용하도록 설정합니다.

### <a name="presets"></a>기본 설정

추적을 제대로 분석하려면 고유한 워크플로 및 기본 데이터 표시를 파악해야 합니다. 하지만 ARR 관련 이벤트에 대한 간략한 개요를 얻을 수 있도록 *Tools/ETLProfiles* 폴더에 사전 설정 파일 및 Windows 소프트웨어 보호 플랫폼이 포함되어 있습니다. 전체 프로필을 로드하려면 WPA 메뉴 모음에서 *프로필 > 적용...* 을 선택하거나 *내 기본 설정* 패널(*창 > 내 기본 설정*)을 열고 *가져오기* 를 선택합니다. 전자는 아래 이미지와 같이 전체 WPA 구성을 설정합니다. 후자는 사용 가능한 다양한 보기 구성에 대한 기본 설정만 만들고 뷰를 빠르게 열어서 ARR 이벤트 데이터의 특정 부분을 확인할 수 있도록 합니다.

![기본 설정](./media/wpa-arr-trace.png)

위의 이미지는 다양한 ARR 특정 이벤트와 전체 CPU 사용률의 뷰를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

* [서버 쪽 성능 쿼리](../overview/features/performance-queries.md)