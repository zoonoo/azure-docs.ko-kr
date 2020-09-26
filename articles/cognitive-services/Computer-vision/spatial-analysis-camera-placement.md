---
title: 공간 분석 카메라 배치
titleSuffix: Azure Cognitive Services
description: 공간 분석에 사용할 카메라를 설정 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 8e951e6cb18596b19f49bf42179297f656e3fa5f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304300"
---
# <a name="camera-placement-guide"></a>카메라 배치 가이드

이 문서에서는 공간 분석 (공개 미리 보기)에 대 한 카메라 배치 권장 사항을 제공 합니다. 여기에는 모든 포함 된 작업에 대 한 높이, 각도 및 카메라-초점 거리에 대 한 특정 권장 사항 뿐만 아니라 일반 지침도 포함 되어 있습니다. 

> [!NOTE]
> 이 가이드는 Axis M3045 카메라를 위한 것입니다. 이 카메라는 해상도 1920 x 1080, 106도 가로 필드 보기, 59 학위 세로 필드 및 고정 2.8 mm 초점 길이를 사용 합니다. 아래 원칙은 모든 카메라에 적용 되지만 카메라 높이 및 카메라-중심 거리에 대 한 구체적인 지침은 다른 카메라에 사용 하도록 조정 해야 합니다. 

## <a name="general-guidelines"></a>일반 지침

공간 분석을 위해 카메라의 위치를 지정 하는 경우 다음과 같은 일반적인 지침을 고려 하세요.

* **조명 높이입니다.** 설비에서 카메라를 차단 하지 않도록 카메라를 조명 설비 아래에 배치 합니다.
* **없도록.** 방해 하지 않는지 카메라 보기를 방지 하려면 극 지방, signage, 보류, 벽 및 기존 LP 카메라와 같은 장애물을 적어 두세요.
* **환경 backlighting.** 옥외 backlighting 카메라 이미지 품질에 영향을 줍니다. 심각한 backlighting 조건을 방지 하려면 외부 연결 windows 및 투명 도어에서 카메라를 전달 하지 않도록 합니다.
* **로컬 개인 정보 취급 방침 규칙 및 규정.** 지역 규정에 따라 카메라에서 캡처할 수 있는 기능이 제한 될 수 있습니다. 카메라를 배치 하기 전에 로컬 규칙 및 규정을 이해 하 고 있어야 합니다.
* **구조를 작성 합니다.** HVAC, 스프링클러 및 기존 전선이 카메라의 하드 탑재를 제한할 수 있습니다.
* **케이블 관리.** 계획 된 카메라 탑재 위치의 이더넷 케이블을 PoE (Power Over Internet) 스위치로 라우팅할 수 있는지 확인 합니다.

## <a name="height-focal-point-distance-and-angle"></a>높이, 면 점 거리 및 각도

공간 분석을 위해 카메라를 설치 하는 방법을 결정할 때 세 가지 사항을 고려해 야 합니다.
- 카메라 높이
- 카메라-중심 지점 거리
- 바닥 평면을 기준으로 하는 카메라의 각도입니다.

가능 하면 대부분의 사용자가 보기의 카메라 필드와 관련 하 여 이동 하는 방향 (사람 탐색 방향)을 알고 있어야 합니다. 이 방향은 시스템 성능에 중요 합니다.

![방향으로 이동 하는 사용자의 이미지](./media/spatial-analysis/person-walking-direction.png)

다음 그림에서는 사람 탐색 방향에 대 한 권한 상승 보기를 보여 줍니다.

![권한 상승 및 계획 보기](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>카메라 높이

일반적으로 카메라는 접지에서 12-14 피트를 탑재 해야 합니다. 이 범위 내에서 카메라 탑재를 계획할 때 카메라 보기에 영향을 줄 수 있는 장애물 (예: 보류 중, 불 광원, 내어쓰기 signage 및 표시)을 고려 하 여 필요에 따라 높이를 조정 합니다.

## <a name="camera-to-focal-point-distance"></a>카메라-중심 지점 거리

_카메라-초점면 거리_ 는 접지 지점 (또는 카메라 이미지의 중심)에서 카메라까지 측정 된 카메라 까지의 선형 거리입니다.

![카메라-중심 점-거리](./media/spatial-analysis/camera-focal-point.png)

이 거리는 바닥 평면에서 측정 됩니다.

![바닥에서 중심 지점 거리를 측정 하는 방법](./media/spatial-analysis/camera-focal-point-floor-plane.png)

위에서는 다음과 같습니다.

![위에서의 중심 지점 거리를 측정 하는 방법](./media/spatial-analysis/camera-focal-point-above.png)

아래 표를 사용 하 여 특정 장착 높이를 기준으로 초점 점에서 카메라 거리를 확인 합니다. 이러한 거리는 최적의 배치를 위한 것입니다. 일부 최대값이는 높이를 제한할 수 있으므로이 표는 12 '-14 ' 권장 사항 아래에 있는 지침을 제공 합니다.

| 카메라 높이 | 카메라-초점 거리 (최소/최대) |  
| ------------- | ---------------------------------------- |  
| 20cm(8            | 10 '-13 '                                  |  
| 5-10           | 7 '-13 '                                   |  
| 10           | 10 '-17 '                                  |  
| 14           | 11 '-18 '                                  |  
| x           | 12 '-22 '                                  |  
| 720           | 15 '-30 '                                  |  

다음 그림은 가장 가깝거나 가장 가까운 카메라에서 가장 가까운 거리 까지의 카메라 보기를 시뮬레이션 합니다.

| 가장 가까운                                      | 떨어진                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![가장 가까운 카메라-중심 지점 거리](./media/spatial-analysis/focal-point-closest.png) | ![가장 먼 카메라-중심 지점 거리](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>카메라 각도 탑재 범위

이 섹션에서는 허용 되는 카메라 각도 탑재 범위에 대해 설명 합니다. 이러한 탑재 범위에는 최적의 배치를 위한 허용 범위가 표시 됩니다.

### <a name="line-configuration"></a>줄 구성

다음 표에서는 **cognitiveservices account spatialanalysis-personcrossingline** 작업에 대해 구성 된 카메라에 대 한 권장 사항을 보여 줍니다. 

| 카메라 높이 | 카메라-중심 지점 거리 | 최적의 카메라 탑재 각도 (최소/최대) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 20cm(8            | 되었는지                             | +/-40 °                                     |  
| 5-10           | 5-10                            | +/-30 °                                     |  
| 10           | /10                            | +/-20 °                                     |  
| x           | 개가                            | +/-10 °                                     |  
| 720           | 가로                            | +/-10 °                                     |  

다음 그림에서는 cognitiveservices account를 사용 하 여 **spatialanalysis-personcrossingline** 를 사용 하는 데 가장 왼쪽 (-) 및 가장 오른쪽 (+) 탑재 각도 권장 사항을 사용 하 여 카메라 보기를 시뮬레이션 합니다.

| 맨 왼쪽 보기                | 오른쪽 보기                |  
| ---------------------------- | ----------------------------- |  
| ![가장 왼쪽 카메라 각도](./media/spatial-analysis/camera-angle-left.png) | ![가장 오른쪽 카메라 각도](./media/spatial-analysis/camera-angle-right.png) |  

다음 그림은의 눈 모양 보기에서 카메라 배치 및 장착 각도를 보여 줍니다.

![조감도](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>영역 구성

카메라를 10mbps 이상으로 이동 하 여 대상 영역이 충분히 크기를 보장 하는 것이 좋습니다. 

영역이 벽 또는 선반와 같은 장애물 옆에 있는 경우 다음 그림에 표시 된 것 처럼 허용 가능한 120 각도 범위 내에서 대상의 지정 된 거리 내에 카메라를 탑재 합니다.

![허용 카메라 각도](./media/spatial-analysis/camera-angle-acceptable.png)

다음 그림에서는 선반 옆에 있는 영역의 왼쪽 및 오른쪽 카메라 보기에 대 한 시뮬레이션을 제공 합니다.

| 왼쪽 보기        | 오른쪽 보기        |  
| ---------------- | ----------------- |  
| ![왼쪽 보기](./media/spatial-analysis/end-cap-left.png) | ![오른쪽 보기](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>큐

**Cognitiveservices account spatialanalysis-personcount**, **cognitiveservices account** **, spatialanalysis 및 persondistance 기술은 큐** 를 모니터링 하는 데 사용할 수 있습니다. 최적 큐 데이터 품질의 경우 큐에 있는 사람들의 폐색를 최소화 하 고 큐 위치가 시간에 따라 일정 하 게 유지 되도록 retractable 벨트 장벽을 선호 합니다.

![Retractable 벨트 큐](./media/spatial-analysis/retractable-belt-queue.png)

이러한 종류의 장애물은 큐를 통해 시스템에서 통찰력을 극대화 하기 위해 불투명 장벽 보다 선호 됩니다.

큐에는 선형 및 지그재그의 두 가지 유형이 있습니다.

다음 그림은 선형 큐에 대 한 권장 사항을 보여 줍니다.

![선형 큐 권장 사항](./media/spatial-analysis/camera-angle-linear-queue.png)

다음 그림에서는 선형 큐의 왼쪽 및 오른쪽 카메라 보기에 대 한 시뮬레이션을 제공 합니다. 카메라를 큐의 반대쪽에 탑재할 수 있습니다.

| 왼쪽 보기                          | 오른쪽 보기                          |  
| ---------------------------------- | ----------------------------------- |  
| ![선형 큐의 왼쪽 각도](./media/spatial-analysis/camera-angle-linear-left.png) | ![선형 큐의 오른쪽 각도](./media/spatial-analysis/camera-angle-linear-right.png) |  

지그재그 큐의 경우 다음 그림과 같이 카메라를 큐 라인 방향에 직접 배치 하지 않는 것이 좋습니다. 그림에서 4 개의 예제 카메라 위치는 각 방향에서 +/-15도의 허용 되는 편차를 사용 하는 이상적인 뷰를 제공 합니다.

다음 그림은 지그재그 대기열의 이상적인 위치에 배치 된 카메라에서 보기를 시뮬레이션 합니다.

| 보기 1        | 보기 2        |  
| ------------- | ------------- |  
| ![보기 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![보기 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 보기 3 |  보기 4 |  
| ---- | ----  |
| ![보기 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![보기 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>유기적 큐

유기적 queue lines 유기적으. 큐의 이러한 스타일은 큐가 2-3 명의 사용자를 초과 하지 않는 경우와 영역 정의 내에서 줄 양식을 구성 하는 경우에 허용 됩니다. 큐 길이가 일반적으로 2-3 명 보다 많은 경우 retractable 벨트 장벽을 사용 하 여 큐 방향을 안내 하 고 영역 정의 내에서 줄 형태를 확인 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [사용자를 계산 하는 웹 응용 프로그램 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [영역 및 줄 배치 가이드](spatial-analysis-zone-line-placement.md)
