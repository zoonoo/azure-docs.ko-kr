---
title: 서버 쪽 성능 쿼리
description: API 호출을 통해 서버 쪽 성능 쿼리를 수행 하는 방법
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682027"
---
# <a name="server-side-performance-queries"></a>서버 쪽 성능 쿼리

서버에서 좋은 렌더링 성능은 안정적인 프레임 속도와 좋은 사용자 환경에 매우 중요 합니다. 서버에서 성능 특징을 신중 하 게 모니터링 하 고 필요한 경우 최적화 하는 것이 중요 합니다. 전용 API 함수를 통해 성능 데이터를 쿼리할 수 있습니다.

렌더링 성능이 가장 중요 한 것은 모델 입력 데이터입니다. [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)에 설명 된 대로 입력 데이터를 수정할 수 있습니다.

클라이언트 쪽 응용 프로그램 성능에도 병목 현상이 발생할 수 있습니다. 클라이언트 쪽 성능에 대 한 심층 분석을 수행 하려면 [성능 추적](../../how-tos/performance-tracing.md)을 수행 하는 것이 좋습니다.

## <a name="clientserver-timeline"></a>클라이언트/서버 타임 라인

다양 한 대기 시간 값에 대해 자세히 살펴보기 전에 타임 라인의 클라이언트와 서버 간 동기화 지점에 대해 확인 하는 것이 좋습니다.

![파이프라인 타임 라인](./media/server-client-timeline.png)

다음 그림에서는이 방법을 보여 줍니다.

* 클라이언트는 상수 60-Hz 프레임 속도 (16.6 밀리초 마다)로 *포즈 예상치* 를 시작 합니다.
* 그런 다음 서버는 포즈에 따라 렌더링을 시작 합니다.
* 서버에서 인코딩된 비디오 이미지를 다시 보냅니다.
* 클라이언트는 이미지를 디코딩하고 그 위에 CPU 및 GPU 작업을 수행한 다음 이미지를 표시 합니다.

## <a name="frame-statistics-queries"></a>프레임 통계 쿼리

프레임 통계는 대기 시간과 같은 마지막 프레임에 대 한 개략적인 정보를 제공 합니다. `FrameStatistics` 구조에 제공 된 데이터는 클라이언트 쪽에서 측정 되므로 API는 동기 호출입니다.

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

검색 `FrameStatistics` 된 개체는 다음 멤버를 포함 합니다.

| 멤버 | 설명 |
|:-|:-|
| latencyPoseToReceive | 클라이언트 응용 프로그램에서이 포즈의 서버 프레임을 완전히 사용할 수 있을 때까지 클라이언트 장치에 대 한 카메라 포즈 예측의 대기 시간입니다. 이 값에는 네트워크 왕복, 서버 렌더링 시간, 비디오 디코드 및 지터 보정이 포함 됩니다. **위의 그림에서 간격 1을** 참조 하세요.|
| latencyReceiveToPresent | 클라이언트 앱이 CPU에 대해 PresentFrame를 호출할 때까지 받은 원격 프레임의 가용성에 대 한 대기 시간입니다. |
| latencyPresentToDisplay  | 디스플레이 표시등이 켜 지 면 CPU에서 프레임을 표시 하는 대기 시간입니다. 이 값에는 클라이언트 GPU 시간, OS에서 수행 하는 모든 프레임 버퍼링, 하드웨어 재 프로젝션 및 장치 종속 디스플레이 검색 시간이 포함 됩니다. **위의 그림에서 간격 2를** 참조 하세요.|
| timeSinceLastPresent | CPU에서 PresentFrame에 대 한 후속 호출 사이의 시간입니다. 표시 지속 시간 보다 큰 값 (예: 60-Hz 클라이언트 장치에서 16.6 밀리초)은 클라이언트 응용 프로그램에서 CPU 워크 로드를 완료 하지 못하는 원인이 되는 문제를 나타냅니다. **위의 그림에서 간격 3을** 참조 하세요.|
| videoFramesReceived | 마지막 1 초 동안 서버에서 받은 프레임 수입니다. |
| videoFrameReusedCount | 장치에서 두 번 이상 사용 된 마지막 1 초 동안 수신 된 프레임의 수입니다. 0이 아닌 값은 네트워크 지터 또는 과도 한 서버 렌더링 시간 때문에 프레임을 다시 사용 하 고 다시 프로젝션 했음을 의미 합니다. |
| videoFramesSkipped | 새 프레임이 도착 하 여 디코딩된 마지막 초당 표시 된 프레임의 수입니다. 0이 아닌 값은 네트워크 jittering 여러 프레임을 지연 시킨 후 클라이언트 장치에 버스트로 함께 도착 했음을 의미 합니다. |
| videoFramesDiscarded | **VideoFramesSkipped**와 매우 비슷하지만 보류 중인 모든 포즈와 더 이상 상관 관계를 지정할 수 없기 때문에 무시 됩니다. 이 경우 심각한 네트워크 경합이 발생 합니다.|
| videoFrameMinDelta | 마지막 1 초 동안 도착 한 연속 된 두 프레임 사이의 최소 시간입니다. VideoFrameMaxDelta와 함께이 범위를 사용 하면 네트워크 또는 비디오 코덱이 발생 한 지터를 나타낼 수 있습니다. |
| videoFrameMaxDelta | 마지막 1 초 동안 도착 한 연속 된 두 프레임 사이의 최대 시간입니다. VideoFrameMinDelta와 함께이 범위를 사용 하면 네트워크 또는 비디오 코덱이 발생 한 지터를 나타낼 수 있습니다. |

모든 대기 시간 값의 합은 일반적으로 60 Hz에서 사용 가능한 프레임 시간 보다 훨씬 큽니다. 그림에 표시 된 것 처럼 여러 프레임이 병렬로 진행 되 고 새 프레임 요청이 원하는 프레임 속도로 시작 되기 때문에이는 정상입니다. 그러나 대기 시간이 너무 커지면 [지연 단계 다시 프로젝션](../../overview/features/late-stage-reprojection.md)의 품질에 영향을 주며 전반적인 환경을 손상 시킬 수 있습니다.

`videoFramesReceived`, `videoFrameReusedCount`및 `videoFramesDiscarded` 은 네트워크 및 서버 성능을 측정 하는 데 사용할 수 있습니다. `videoFramesReceived` 가 낮고 `videoFrameReusedCount` 가 높으면 네트워크 정체 또는 서버 성능이 저하 될 수 있습니다. 높은 `videoFramesDiscarded` 값은 네트워크 정체도 나타냅니다.

마지막으로`timeSinceLastPresent` `videoFrameMinDelta`,, 및 `videoFrameMaxDelta` 는 들어오는 비디오 프레임 및 로컬 현재 호출의 분산에 대 한 아이디어를 제공 합니다. 분산이 높으면 프레임 율이 가능 합니다.

위의 값은 서버를 사용 하는 정확한 시간을 왕복 값 `latencyPoseToReceive`에서 빼서는 안 되므로 순수한 네트워크 대기 시간 (그림의 빨간색 화살표)을 명확 하 게 표시 하지 않습니다. 전체 대기 시간의 서버측 부분은 클라이언트에서 사용할 수 없는 정보입니다. 그러나 다음 단락은 서버에서 추가 입력을 통해이 값을 대략적으로 표시 하 고 `networkLatency` 값을 통해 노출 하는 방법을 설명 합니다.

## <a name="performance-assessment-queries"></a>성능 평가 쿼리

*성능 평가 쿼리* 는 서버의 CPU 및 GPU 워크 로드에 대 한 자세한 정보를 제공 합니다. 데이터가 서버에서 요청 되기 때문에 성능 스냅숏 쿼리는 일반적인 비동기 패턴을 따릅니다.

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

`FrameStatistics` 개체와 달리 개체는 서버 `PerformanceAssessment` 쪽 정보를 포함 합니다.

| 멤버 | 설명 |
|:-|:-|
| timeCPU | 프레임당 평균 서버 CPU 시간 (밀리초) |
| timeGPU | 프레임당 평균 서버 GPU 시간 (밀리초) |
| utilizationCPU | 총 서버 CPU 사용률 (%) |
| utilizationGPU | 총 서버 GPU 사용률 (%) |
| memoryCPU | 총 서버 주 메모리 사용률 (%) |
| memoryGPU | 서버 GPU 비율의 총 전용 비디오 메모리 사용률 |
| networkLatency | 대략적인 평균 왕복 네트워크 대기 시간 (밀리초)입니다. 위의 그림에서이 값은 빨간색 화살표의 합계에 해당 합니다. 값은의 `latencyPoseToReceive` `FrameStatistics`값에서 실제 서버 렌더링 시간을 빼서 계산 합니다. 이 근사값은 정확한 것은 아니지만 클라이언트에서 계산 되는 대기 시간 값 으로부터 격리 된 네트워크 대기 시간을 나타냅니다. |
| polygonsRendered | 하나의 프레임에서 렌더링 된 삼각형의 수입니다. 이 숫자에는 렌더링 하는 동안 나중에 추출 삼각형도 포함 됩니다. 즉,이 수는 서로 다른 카메라 위치에서 다를 수 있지만 성능은 삼각형 고르기 속도에 따라 현저 하 게 달라질 수 있습니다.|

값을 평가 하는 데 도움이 되도록 각 부분은 **좋은**, **양호**, **Mediocre**또는 **불량**등의 품질 분류를 제공 합니다.
이 평가 메트릭은 서버의 상태에 대 한 대략적인 표시를 제공 하지만 절대로 표시 되어서는 안 됩니다. 예를 들어 GPU 시간에 대해 ' mediocre ' 점수가 표시 된다고 가정 합니다. 전체 프레임 시간 예산에 대 한 제한에 근접 하 여 mediocre 간주 됩니다. 그러나 복잡 한 모델을 렌더링 하기 때문에이 경우에는 좋은 값일 수 있습니다.

## <a name="statistics-debug-output"></a>통계 디버그 출력

클래스 `ARRServiceStats` 는 프레임 통계 및 성능 평가 쿼리를 모두 래핑하고 통계를 집계 된 값 또는 미리 작성 된 문자열로 반환 하는 편리한 기능을 제공 합니다. 다음 코드는 클라이언트 응용 프로그램에서 서버 쪽 통계를 표시 하는 가장 쉬운 방법입니다.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

위의 코드는 텍스트 레이블을 다음 텍스트로 채웁니다.

![ArrServiceStats 문자열 출력](./media/arr-service-stats.png)

API `GetStatsString` 는 모든 값에 대 한 문자열의 형식을 지정 하지만 `ARRServiceStats` 인스턴스에서 프로그래밍 방식으로 각 단일 값을 쿼리할 수도 있습니다.

또한 시간에 따라 값을 집계 하는 멤버의 변형이 있습니다. 접미사 `*Avg`, `*Max`또는 `*Total`를 포함 하는 멤버를 참조 하십시오. 멤버 `FramesUsedForAverage` 는이 집계에 사용 된 프레임 수를 나타냅니다.

## <a name="next-steps"></a>다음 단계

* [성능 추적 만들기](../../how-tos/performance-tracing.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
