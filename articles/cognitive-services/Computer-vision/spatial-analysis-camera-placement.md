---
title: 공간 분석 카메라 배치
titleSuffix: Azure Cognitive Services
description: 공간 분석에 사용할 카메라를 설정하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: f25854c93318cf581843e2d484a4265475441271
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744362"
---
# <a name="camera-placement-guide"></a>카메라 배치 가이드

이 문서에서는 공간 분석(공개 미리 보기)에 대한 카메라 배치 권장 사항을 제공합니다. 여기에는 모든 포함된 작업에 대한 높이, 각도 및 카메라-초점 거리에 대한 특정 권장 사항 뿐만 아니라 일반 지침도 포함되어 있습니다. 

> [!NOTE]
> 이 가이드는 Axis M3045-V 카메라용으로 설계되었습니다. 이 카메라는 해상도 1920x1080, 106도 수평 시야, 59도 수직 시야 및 고정 2.8mm 초점 거리를 사용합니다. 아래 원칙은 모든 카메라에 적용되지만 카메라 높이 및 카메라-초점 거리에 대한 구체적인 지침은 다른 카메라에 사용하도록 조정해야 합니다. 

## <a name="general-guidelines"></a>일반 지침

공간 분석을 위해 카메라의 위치를 지정하는 경우 다음과 같은 일반적인 지침을 고려하세요.

* **조명 높이** 픽스쳐에서 카메라를 차단하지 않도록 카메라를 조명 픽스쳐 아래에 배치합니다.
* **장애물** 카메라 시야를 방해하지 않으려면 폴, 간판, 선반, 벽 및 기존 LP 카메라와 같은 장애물을 기록해 두십시오.
* **환경 역광 조명** 야외 역광 조명은 카메라 이미지 품질에 영향을 줍니다. 심각한 역광 조명 상태를 방지하려면 외부 방향의 창과 유리문에 카메라를 들이대지 마십시오.
* **로컬 개인 정보 규칙 및 규정** 로컬 규정에 따라 카메라에서 캡처할 수 있는 기능이 제한될 수 있습니다. 카메라를 배치하기 전에 로컬 규칙 및 규정을 이해하고 있어야 합니다.
* **구조 작성** HVAC, 스프링클러 및 기존 배선은 카메라의 하드 마운팅을 제한할 수 있습니다.
* **케이블 관리** 계획 된 카메라 탑재 위치의 이더넷 케이블을 PoE(Power Over Internet) 스위치로 라우팅할 수 있는지 확인합니다.

## <a name="height-focal-point-distance-and-angle"></a>높이, 초점 거리 및 각도

공간 분석을 위해 카메라를 설치하는 방법을 결정할 때 세 가지 사항을 고려해야 합니다.
- 카메라 높이
- 카메라-초점 거리
- 바닥 평면을 기준으로 하는 카메라의 각도

가능하면 대부분의 사람이 카메라 시야와 관련하여 걷는 방향(사람이 걷는 방향)을 알고 있어야 합니다. 이 방향은 시스템 성능에 중요합니다.

![한 방향으로 걷는 사람의 이미지](./media/spatial-analysis/person-walking-direction.png)

다음 그림에서는 사람이 걷는 방향에 대한 권한 상승 보기를 보여 줍니다.

![권한 상승 및 계획 보기](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>카메라 높이

일반적으로 카메라는 지면에서 12-14피트 떨어진 곳에 탑재되어야 합니다. 얼굴 마스크 검색의 경우 카메라를 지면에서 8-12피트 떨어진 곳에 탑재하는 것이 좋습니다. 이 범위 내에서 카메라 탑재를 계획할 때 카메라 보기에 영향을 줄 수 있는 장애물(예: 선반, 매달린 조명, 매달린 간판 및 디스플레이)을 고려한 다음, 필요에 따라 높이를 조정합니다.

## <a name="camera-to-focal-point-distance"></a>카메라-초점 거리

_카메라-초점 거리_ 는 초점(또는 카메라 이미지의 중심)에서 지면에서 측정된 카메라까지의 선형 거리입니다.

![카메라-초점 거리](./media/spatial-analysis/camera-focal-point.png)

이 거리는 바닥 평면에서 측정됩니다.

![바닥에서의 카메라-초점 거리를 측정하는 방법](./media/spatial-analysis/camera-focal-point-floor-plane.png)

위에서는 다음과 같습니다.

![위에서의 카메라-초점 거리를 측정하는 방법](./media/spatial-analysis/camera-focal-point-above.png)

아래 표를 사용하여 특정 장착 높이를 기준으로 초점에서 카메라 거리를 확인합니다. 이러한 거리는 최적의 배치를 위한 것입니다. 일부 천장은 높이를 제한할 수 있으므로 이 표는 12'-14' 권장 사항 아래에 있는 지침을 제공합니다. 얼굴 마스크 검색의 경우 권장되는 카메라-초점 거리(최소/최대)는 8'~12' 사이의 카메라 높이에 대해 4'-10'입니다.

| 카메라 높이 | 카메라-초점 거리(최소/최대) |  
| ------------- | ---------------------------------------- |  
| 8'            | 4.6'-8'                                  |  
| 10'           | 5.8'-10'                                 |  
| 12'           | 7'-12'                                   |  
| 14'           | 8'-14''                                  |  
| 16'           | 9.2'-16'                                 |  
| 20'           | 11.5'-20'                                |  

다음 그림에서는 가장 가깝고 가장 먼 카메라-초점 거리에서 카메라 보기를 시뮬레이션합니다.

| 가장 가까운 거리                                      | 가장 먼 거리                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![가장 가까운 카메라-초점 거리](./media/spatial-analysis/focal-point-closest.png) | ![가장 먼 카메라-초점 거리](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>카메라 각도 탑재 범위

이 섹션에서는 허용되는 카메라 각도 탑재 범위에 대해 설명합니다. 이러한 탑재 범위에는 최적의 배치를 위한 허용 범위가 표시됩니다.

### <a name="line-configuration"></a>회선 구성

**cognitiveservices.vision.spatialanalysis-personcrossingline** 작업의 경우 정확도를 최대화하기 위한 최적의 카메라 탑재 각도는 +/-5°입니다.

얼굴 마스크 검색의 경우 +/-30도는 8'~12' 사이의 카메라 높이를 위한 최적의 카메라 탑재 각도입니다.

다음 그림에서는 출입구에서 입구 카운팅을 수행하기 위해 **cognitiveservices.vision.spatialanalysis-personcrossingline** 를 사용하는 데 가장 왼쪽(-) 및 가장 오른쪽(+) 탑재 각도 권장 사항을 사용하여 카메라 보기를 시뮬레이션합니다.

| 가장 왼쪽 보기                | 가장 오른쪽 보기                |  
| ---------------------------- | ----------------------------- |  
| ![가장 왼쪽 카메라 각도](./media/spatial-analysis/camera-angle-left.png) | ![가장 오른쪽 카메라 각도](./media/spatial-analysis/camera-angle-right.png) |  

다음 그림에서는 카메라 배치 및 탑재 각도를 조감도 관점에서 보여 줍니다.

![조감도](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>영역 구성

덮인 영역이 충분히 넓을 수 있도록 지상 10피트 이상 높이에 카메라를 설치하는 것이 좋습니다. 

영역이 벽 또는 선반과 같은 장애물 옆에 있는 경우 다음 그림에 표시된 것처럼 허용 가능한 120도 각도 범위 내에서 대상의 지정된 거리 내에 카메라를 탑재합니다.

![허용 가능한 카메라 각도](./media/spatial-analysis/camera-angle-acceptable.png)

다음 그림에서는 선반 옆에 있는 영역의 왼쪽 및 오른쪽 카메라 보기에 대한 시뮬레이션을 제공합니다.

| 왼쪽 보기        | 오른쪽 보기        |  
| ---------------- | ----------------- |  
| ![왼쪽 보기](./media/spatial-analysis/end-cap-left.png) | ![오른쪽 보기](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>큐

**cognitiveservices.vision.spatialanalysis-personcount**, **cognitiveservices.vision.spatialanalysis-persondistance** 및 **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** 기술은 큐를 모니터링하는 데 사용할 수 있습니다. 최적의 큐 데이터 품질의 경우 접이식 벨트 장벽이 선호되어 큐의 사람 폐색을 최소화하고 시간이 지남에 따라 큐 위치가 일관되도록 보장합니다.

![접이식 벨트 큐](./media/spatial-analysis/retractable-belt-queue.png)

이러한 종류의 장벽은 시스템에서 얻은 인사이트의 정확도를 최대화하기 위해 큐 형성에 대한 불투명 장벽보다 선호됩니다.

큐에는 선형 및 지그재그의 두 가지 유형이 있습니다.

다음 그림은 선형 큐에 대한 권장 사항을 보여 줍니다.

![선형 큐 권장 사항](./media/spatial-analysis/camera-angle-linear-queue.png)

다음 그림에서는 선형 큐의 왼쪽 및 오른쪽 카메라 보기에 대한 시뮬레이션을 제공합니다. 카메라를 큐의 반대쪽에 탑재할 수 있습니다.

| 왼쪽 보기                          | 오른쪽 보기                          |  
| ---------------------------------- | ----------------------------------- |  
| ![선형 큐의 왼쪽 각도](./media/spatial-analysis/camera-angle-linear-left.png) | ![선형 큐의 오른쪽 각도](./media/spatial-analysis/camera-angle-linear-right.png) |  

지그재그 큐의 경우 다음 그림과 같이 카메라를 큐 라인 방향에 직접 배치하지 않는 것이 좋습니다. 그림에서 4개의 예제 카메라 위치는 각 방향에서 +/-15도의 허용되는 편차를 사용하는 이상적인 뷰를 제공합니다.

다음 그림은 지그재그 큐의 이상적인 위치에 배치된 카메라에서 보기를 시뮬레이션합니다.

| 보기 1        | 보기 2        |  
| ------------- | ------------- |  
| ![보기 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![보기 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 보기 3 |  보기 4 |  
| ---- | ----  |
| ![보기 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![보기 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>유기적 큐

유기적 큐 선은 유기적으로 형성됩니다. 큐가 2-3명 이상 형성되지 않고 영역 정의 내에 줄이 형성되는 경우 이 스타일의 큐를 사용할 수 있습니다. 큐 길이가 일반적으로 2-3명보다 많은 경우 접이식 벨트 장벽을 사용하여 큐 방향을 안내하고 영역 정의 내에서 줄을 형성하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [인원수를 세는 웹 애플리케이션 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [영역 및 선 배치 가이드](spatial-analysis-zone-line-placement.md)
