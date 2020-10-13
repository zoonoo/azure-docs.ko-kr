---
title: 공간 분석 영역 및 선 배치
titleSuffix: Azure Cognitive Services
description: 공간 분석을 사용 하 여 영역 및 줄을 설정 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939413"
---
# <a name="zone-and-line-placement-guide"></a>영역 및 줄 배치 가이드

이 문서에서는 공간 분석 작업을 위한 영역 및 줄을 정의 하 여 공간에서 peoples 이동에 대 한 정확한 분석을 수행 하는 방법에 대 한 지침을 제공 합니다. 이는 모든 작업에 적용 됩니다. 

영역 및 줄은 JSON SPACEANALYSIS_CONFIG 매개 변수를 사용 하 여 정의 됩니다. 자세한 내용은 [공간 분석 작업](spatial-analysis-operations.md) 문서를 참조 하세요.

## <a name="guidelines-for-drawing-zones"></a>영역 그리기에 대 한 지침

모든 공백은 서로 다릅니다. 필요에 따라 위치 또는 크기를 업데이트 해야 합니다.

카메라 보기의 특정 섹션을 보려면 관심 있는 특정 바닥 영역을 포함 하지만 관심 있지 않은 다른 영역은 포함 하지 않을 수 있는 가장 큰 영역을 만듭니다. 이렇게 하면 수집 되는 데이터의 정확도가 높아지고 추적 하지 않으려는 영역에서 가양성이 방지 됩니다. 다각형의 모퉁이를 배치할 때 주의 해야 하며 추적 하려는 영역 밖에 있지 않은지 확인 하세요.  

### <a name="example-of-a-well-shaped-zone"></a>잘 된 모양의 영역 예

영역은 각에 지를 따라 세 명의 사용자를 수용 하 고 관심 영역에 초점을 맞출 수 있도록 충분히 커야 합니다. 공간 분석은 피트를 영역에 배치 하는 사람을 식별 하므로 2D 이미지에서 영역을 그릴 때 영역을 바닥의 카펫로 가정 합니다.

![잘 모양의 영역](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>잘 모양이 아닌 영역 예

다음 예제에서는 잘못 된 모양의 영역을 보여 줍니다. 이 예에서 관심 영역은 *게임 시간* 표시 앞의 공간입니다.

**영역이 바닥에 있지 않습니다.**

![셰이프 영역이 잘못 되었습니다.](./media/spatial-analysis/zone-not-on-floor.png) 

**영역이 너무 작습니다.**

![영역이 너무 작습니다.](./media/spatial-analysis/zone-too-small.png)

**영역에 표시 되는 영역을 완전히 캡처하지 않습니다.**

![영역에서 끝 캡 주위의 영역을 완전히 캡처하지 않습니다.](./media/spatial-analysis/zone-bad-capture.png)

**영역이 카메라 이미지의 가장자리에 너무 가깝습니다. 올바른 디스플레이를 캡처하지 않습니다.**

![영역이 카메라 이미지의 가장자리에 너무 가깝습니다. 올바른 디스플레이를 캡처하지 않습니다.](./media/spatial-analysis/zone-edge.png)

**영역을 부분적으로 차단 하므로 사용자와 층이 완전히 표시 되지 않습니다.**

![영역을 부분적으로 차단 하므로 사람들이 완전히 볼 수 없습니다.](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>잘 모양의 선 예

줄은 전체 입구를 수용할 만큼 길어야 합니다. 공간 분석은 피트를 가로지르는 사용자를 식별 하므로 2D 이미지에서 선을 그릴 때 바닥에 있는 것 처럼 그리는 것으로 가정 합니다. 

가능 하면 실제 입구 보다 더 넓은 줄을 확장 합니다. 이로 인해 추가 교차가 발생 하지 않는 경우 (아래 이미지에서와 같이 벽에 대 한 선) 확장 합니다.

![잘 모양의 선](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>잘 모양이 아닌 줄의 예

다음 예에서는 잘못 정의 된 줄을 보여 줍니다.

**줄은 바닥에서 전체 항목 방식을 다루지 않습니다.**

![줄이 바닥에서 전체 항목 방식에 포함 되지 않습니다.](./media/spatial-analysis/zone-line-bad-coverage.png)

**줄이 너무 높고 전체 도어를 포함 하지 않습니다.**

![줄이 너무 높고 전체 도어를 포함 하지 않습니다.](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>다음 단계

* [사용자를 계산 하는 웹 응용 프로그램 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [로깅 및 문제 해결](spatial-analysis-logging.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
