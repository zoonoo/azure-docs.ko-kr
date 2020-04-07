---
title: 서버 측 성능 쿼리
description: API 호출을 통해 서버 측 성능 쿼리를 수행하는 방법
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682027"
---
# <a name="server-side-performance-queries"></a>서버 측 성능 쿼리

서버에서의 좋은 렌더링 성능은 안정적인 프레임 속도와 좋은 사용자 환경을 위해 매우 중요합니다. 서버의 성능 특성을 주의 깊게 모니터링하고 필요한 경우 최적화하는 것이 중요합니다. 성능 데이터는 전용 API 함수를 통해 쿼리할 수 있습니다.

렌더링 성능에 가장 큰 영향을 미치는 것은 모델 입력 데이터입니다. [모델 변환 구성에](../../how-tos/conversion/configure-model-conversion.md)설명된 대로 입력 데이터를 조정할 수 있습니다.

클라이언트 측 응용 프로그램 성능도 병목 현상일 수 있습니다. 클라이언트 측 성능에 대한 심층 분석을 위해 [성능 추적을](../../how-tos/performance-tracing.md)하는 것이 좋습니다.

## <a name="clientserver-timeline"></a>클라이언트/서버 타임라인

다양한 대기 시간 값에 대해 자세히 살펴보기 전에 타임라인에서 클라이언트와 서버 간의 동기화 지점을 살펴보는 것이 좋습니다.

![파이프라인 타임라인](./media/server-client-timeline.png)

그림은 다음과 같은 방법을 보여줍니다.

* *포즈 추정은* 클라이언트가 일정한 60Hz 프레임 속도(16.6ms마다)로 시작합니다.
* 그런 다음 포즈에 따라 렌더링을 시작합니다.
* 서버가 인코딩된 비디오 이미지를 다시 보냅니다.
* 클라이언트는 이미지를 디코딩하고, 그 위에 일부 CPU 및 GPU 작업을 수행한 다음 이미지를 표시합니다.

## <a name="frame-statistics-queries"></a>프레임 통계 쿼리

프레임 통계는 대기 시간 등 마지막 프레임에 대한 몇 가지 상위 수준 정보를 제공합니다. 구조에 `FrameStatistics` 제공된 데이터는 클라이언트 측에서 측정되므로 API는 동기 호출입니다.

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

검색된 `FrameStatistics` 개체는 다음 멤버를 보유합니다.

| 멤버 | 설명 |
|:-|:-|
| 대기 시간포즈수신 | 이 포즈에 대한 서버 프레임을 클라이언트 응용 프로그램에서 완전히 사용할 수 있도록 카메라 포즈 추정에서 대기 시간입니다. 이 값에는 네트워크 왕복, 서버 렌더링 시간, 비디오 디코딩 및 지터 보정이 포함됩니다. **위의 그림에서 간격 1을 참조하십시오.**|
| 대기 시간수신현재 | 클라이언트 앱이 CPU에서 PresentFrame을 호출할 때까지 수신된 원격 프레임의 가용성에서 대기 시간입니다. |
| 대기 시간현재표시  | 디스플레이가 켜지때까지 CPU에 프레임을 표시하는 대기 시간입니다. 이 값에는 클라이언트 GPU 시간, OS에서 수행하는 프레임 버퍼링, 하드웨어 재투영 및 장치 종속 디스플레이 스캔 시간이 포함됩니다. **위의 그림에서 간격 2를 참조하십시오.**|
| 시간이후마지막현재 | CPU에서 PresentFrame에 대한 후속 호출 사이의 시간입니다. 표시 기간보다 큰 값(예: 60Hz 클라이언트 장치의 16.6ms)은 클라이언트 응용 프로그램이 CPU 워크로드를 제 시간에 완료하지 않아 발생하는 문제를 나타냅니다. **위의 그림에서 간격 3을 참조하십시오.**|
| 비디오프레임 수신 | 마지막 초 동안 서버에서 받은 프레임 수입니다. |
| 비디오프레임재사용카운트 | 장치에서 두 번 이상 사용된 마지막 초의 수신된 프레임 수입니다. 0이 아닌 값은 네트워크 지터 또는 과도한 서버 렌더링 시간으로 인해 프레임을 다시 사용하고 다시 투영해야 함을 나타냅니다. |
| 비디오프레임스크팁 | 디코딩되었지만 최신 프레임이 도착했기 때문에 디스플레이에 표시되지 않은 마지막 초의 수신된 프레임 수입니다. 0이 아닌 값은 네트워크 지터링으로 인해 여러 프레임이 지연된 다음 버스트에서 클라이언트 장치에 함께 도착했음을 나타냅니다. |
| 비디오 프레임 삭제됨 | **videoFramesSkipped와**매우 유사하지만 삭제되는 이유는 프레임이 너무 늦게 들어와서 더 이상 보류 중인 포즈와 상관 관계가 없기 때문입니다. 이 경우 심각한 네트워크 경합이 발생합니다.|
| 비디오프레임민델타 | 마지막 1초 동안 도착하는 두 개의 연속 프레임 사이의 최소 시간입니다. videoFrameMaxDelta와 함께이 범위는 네트워크 또는 비디오 코덱으로 인한 지터를 표시합니다. |
| 비디오프레임맥스델타 | 마지막 1초 동안 도착하는 두 개의 연속 프레임 사이의 최대 시간입니다. videoFrameMinDelta와 함께이 범위는 네트워크 또는 비디오 코덱으로 인한 지터를 표시합니다. |

모든 대기 시간 값의 합은 일반적으로 60Hz에서 사용 가능한 프레임 시간보다 훨씬 큽습니다. 여러 프레임이 병렬로 비행중이며 그림과 같이 새 프레임 요청이 원하는 프레임 속도로 시작되기 때문에 이 방법은 정상입니다. 그러나 대기 시간이 너무 커지면 [후반 단계 재투영의](../../overview/features/late-stage-reprojection.md)품질에 영향을 미치며 전반적인 환경이 손상될 수 있습니다.

`videoFramesReceived``videoFrameReusedCount`을 사용하여 `videoFramesDiscarded` 네트워크 및 서버 성능을 측정할 수 있습니다. 낮고 `videoFramesReceived` `videoFrameReusedCount` 높으면 네트워크 정체 또는 서버 성능 저하를 나타낼 수 있습니다. 값이 `videoFramesDiscarded` 높음은 네트워크 정체를 나타냅니다.

마지막으로,`timeSinceLastPresent`, `videoFrameMinDelta`그리고 `videoFrameMaxDelta` 들어오는 비디오 프레임과 로컬 현재 호출의 차이에 대한 아이디어를 제공합니다. 분산이 높으면 프레임 속도가 불안정합니다.

위의 값 중 어느 것도 서버가 사용 중인 렌더링이 바쁜 정확한 시간을 왕복 값에서 `latencyPoseToReceive`빼야 하기 때문에 순수 네트워크 대기 시간(그림의 빨간색 화살표)을 명확하게 표시하지 않습니다. 전체 대기 시간의 서버 측 부분은 클라이언트에서 사용할 수 없는 정보입니다. 그러나 다음 단락에서는 서버의 추가 입력을 통해 이 값을 근사화하고 `networkLatency` 값을 통해 노출되는 방법을 설명합니다.

## <a name="performance-assessment-queries"></a>성능 평가 쿼리

*성능 평가 쿼리는* 서버의 CPU 및 GPU 워크로드에 대한 보다 자세한 정보를 제공합니다. 서버에서 데이터가 요청되므로 성능 스냅숏을 쿼리하는 것은 일반적인 비동기 패턴을 따릅니다.

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

개체와 `FrameStatistics` 달리 개체에는 `PerformanceAssessment` 서버 측 정보가 포함되어 있습니다.

| 멤버 | 설명 |
|:-|:-|
| 시간 CPU | 프레임당 평균 서버 CPU 시간(밀리초) |
| 타임GPU | 프레임당 평균 서버 GPU 시간(밀리초) |
| 활용도CPU | 총 서버 CPU 사용률%(백분율) |
| 활용GPU | 총 서버 GPU 사용률%로 |
| 메모리CPU | 총 서버 주 메모리 사용률%로 |
| 메모리 GPU | 서버 GPU의 백분율로 총 전용 비디오 메모리 사용률 |
| 네트워크지연 | 밀리초 단위의 대략적인 평균 왕복 네트워크 대기 시간입니다. 위의 그림에서 빨간색 화살표의 합계에 해당 합니다. 이 값은 실제 서버 렌더링 시간을 `latencyPoseToReceive` `FrameStatistics`에서 의 값에서 빼서 계산됩니다. 이 근사치가 정확하지는 않지만 클라이언트에서 계산된 대기 시간 값에서 격리된 네트워크 대기 시간을 표시합니다. |
| 다각형렌더링 | 한 프레임에서 렌더링되는 삼각형 수입니다. 이 숫자에는 렌더링 중에 나중에 컬링되는 삼각형도 포함됩니다. 즉, 이 숫자는 카메라 위치에 따라 크게 다르지 않지만 삼각형 컬링 속도에 따라 성능이 크게 달라질 수 있습니다.|

값을 평가하는 데 도움이, 각 부분은 **좋은**같은 품질 분류와 함께 제공, **좋은,** **평범한,** 또는 **나쁜**.
이 평가 메트릭은 서버의 상태를 대략적인 표시로 제공하지만 절대로 표시해서는 안 됩니다. 예를 들어 GPU 시간에 대한 '평범한' 점수가 표시된다고 가정합니다. 그것은 전체 프레임 시간 예산에 대 한 제한에 가까워지기 때문에 평범한 것으로 간주 됩니다. 그러나 복잡한 모델을 렌더링하기 때문에 좋은 값일 수 있습니다.

## <a name="statistics-debug-output"></a>통계 디버그 출력

클래스는 `ARRServiceStats` 프레임 통계 및 성능 평가 쿼리를 둘 다 둘러싸고 집계된 값또는 미리 빌드된 문자열로 통계를 반환하는 편리한 기능을 제공합니다. 다음 코드는 클라이언트 응용 프로그램에서 서버 측 통계를 표시하는 가장 쉬운 방법입니다.

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

API는 `GetStatsString` 모든 값의 문자열을 포맷하지만 각 단일 값은 `ARRServiceStats` 인스턴스에서 프로그래밍 방식으로 쿼리할 수도 있습니다.

시간에 따라 값을 집계하는 멤버의 변형도 있습니다. 접미사가 `*Avg`있는 멤버를 `*Total`참조하십시오. `*Max` 멤버는 `FramesUsedForAverage` 이 집계에 사용된 프레임 수를 나타냅니다.

## <a name="next-steps"></a>다음 단계

* [성능 추적 생성](../../how-tos/performance-tracing.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
