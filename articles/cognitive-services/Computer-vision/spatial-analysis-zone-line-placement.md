---
title: 공간 분석 영역 및 선 배치
titleSuffix: Azure Cognitive Services
description: 공간 분석을 사용하여 영역 및 선을 설정하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: cfd3bc406407298c6daf7723cb684911d7c9a9cf
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284671"
---
# <a name="zone-and-line-placement-guide"></a>영역 및 선 배치 가이드

이 문서에서는 공간 분석 작업을 위한 영역 및 선을 정의하여 공간에서 사람들의 이동에 대한 정확한 분석을 수행하는 방법에 대한 지침을 제공합니다. 이 지침은 모든 작업에 적용됩니다. 

영역 및 선은 JSON SPACEANALYSIS_CONFIG 매개 변수를 사용하여 정의됩니다. 자세한 내용은 [공간 분석 작업](spatial-analysis-operations.md) 문서를 참조하세요.

## <a name="guidelines-for-drawing-zones"></a>영역 그리기에 대한 지침

모든 공백은 서로 다릅니다. 필요에 따라 위치 또는 크기를 업데이트해야 합니다.

카메라 보기의 특정 섹션을 보려면 관심 있는 특정 바닥 영역을 포함하지만 관심 있지 않은 다른 영역은 포함하지 않을 수 있는 가장 큰 영역을 만듭니다. 이렇게 하면 수집되는 데이터의 정확도가 높아지고 추적하지 않으려는 영역에서 가양성이 방지됩니다. 다각형의 모퉁이를 배치할 때 주의해야 하며 추적하려는 영역 밖에 있지 않은지 확인합니다.  

### <a name="example-of-a-well-shaped-zone"></a>잘 구성된 영역에 대한 예제

영역은 각 에지를 따라 세 명의 사용자를 수용하고 관심 영역에 초점을 맞출 수 있도록 충분히 커야 합니다. 공간 분석은 피트를 영역에 배치하는 사람을 식별하므로 2D 이미지에서 영역을 그릴 때 영역을 바닥의 카펫으로 가정합니다.

![잘 구성된 영역](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>잘 구성되지 않은 영역에 대한 예제

다음 예제에서는 잘못된 모양의 영역을 보여 줍니다. 이러한 예제에서 관심 영역은 *게임 시간* 표시 앞의 공간입니다.

**영역이 바닥에 있지 않습니다.**

![잘 구성되지 않은 영역](./media/spatial-analysis/zone-not-on-floor.png) 

**영역이 너무 작습니다.**

![영역이 너무 작음](./media/spatial-analysis/zone-too-small.png)

**영역이 디스플레이 주변 영역을 완전히 캡처하지 않습니다.**

![영역이 단면 주변 영역을 완전히 캡처하지 않음](./media/spatial-analysis/zone-bad-capture.png)

**영역이 카메라 이미지의 가장자리에 너무 가깝고 오른쪽 디스플레이를 캡처하지 않습니다.**

![영역이 카메라 이미지의 가장자리에 너무 가깝고 오른쪽 디스플레이를 캡처하지 않음](./media/spatial-analysis/zone-edge.png)

**영역은 부분적으로 선반에 의해 차단되므로 사람과 바닥이 완전히 보이지 않습니다.**

![영역은 부분적으로 차단되므로 사람이 완전히 보이지 않음](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>잘 구성된 선에 대한 예제

선은 전체 진입선을 수용할 수 있을 만큼 길어야 합니다. 공간 분석은 선을 넘은 발이 있는 사람을 식별하므로 2D 이미지에 선을 그릴 때 마치 바닥 위에 있는 것처럼 그려지는 것을 상상해 보세요. 

가능하면 실제 진입선보다 더 넓은 선을 확장합니다. 이로 인해 추가 교차가 발생하지 않는 경우(선이 벽면에 있을 때 아래 이미지와 같이) 확장합니다.

![잘 구성된 선](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>잘 구성되지 않은 선에 대한 예제

다음 예제에서는 잘못 정의된 선을 보여 줍니다.

**선은 바닥의 전체 진입로를 포함하지 않습니다.**

![선은 바닥의 전체 진입로를 포함하지 않음](./media/spatial-analysis/zone-line-bad-coverage.png)

**선이 너무 높고 문 전체를 덮지 않습니다.**

![선이 너무 높고 문 전체를 덮지 않음](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>다음 단계

* [인원 수를 세는 웹 애플리케이션 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
